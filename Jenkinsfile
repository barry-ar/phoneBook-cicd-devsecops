/* import shared library */
@Library('jenkins-shared-library')_

pipeline {
    agent none
    stages {
        stage('Check bash syntax') {
            agent { docker { image 'koalaman/shellcheck-alpine:stable' } }
            steps {
			    script{ bashCheck }
            }
        }
        stage('Check yaml syntax') {
            agent { docker { image 'sdesbure/yamllint' } }
            steps {
			    script { yamlCheck }
            }
        }
        stage('Check markdown syntax') {
            agent { docker { image 'ruby:alpine' } }
            steps {
			    script{ markdownCheck }
            }
        }
        stage('Prepare ansible environment') {
            agent any
            environment {
                VAULTKEY = credentials('vaultkey')
                DEVOPSKEY = credentials('devopskey')
            }
            steps {
                sh 'echo \$VAULTKEY > vault.key'
                sh 'cp \$DEVOPSKEY id_rsa'
                sh 'chmod 600 id_rsa'
            }
        }
        stage('Test and deploy the application') {
            agent any
            stages {
               stage("Install ansible role dependencies") {
                   steps {
                       sh 'ansible-galaxy install -r roles/requirements.yml'
                   }
               }
               stage("Ping targeted hosts") {
                   steps {
                       sh 'ansible all -m ping -i hosts --private-key id_rsa'
                   }
               }
               stage("Vérify ansible playbook syntax") {
                   steps {
                       sh 'sudo yum -y install python-pip'
                       sh 'sudo pip install ansible-lint'
                       sh 'ansible-lint -x 306 playbook_phonebook.yml'
                       sh 'echo "${GIT_BRANCH}"'
                   }
               }
               stage("Build docker images on build host") {
                   when {
                      expression { GIT_BRANCH == 'origin/deve' }
                  }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "build" --limit build playbook_phonebook.yml'
                   }
               }
			   stage("Scan docker images phonebook-mysl on build host") {
			       when {
				       expression { GIT_BRANCH == 'origin/deve' }
					}
					steps {
					    sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa attack/clair-scan.yml'
					}
			   }
			   stage("Push docker images from build host") { 
			       when {
				      expression { GIT_BRANCH == 'origin/deve' }
				  }
				   steps {
				       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "push" --limit build playbook_phonebook.yml'
				   }
			   }
	           stage("Deploy on preprod host") {
		           when {	
		              expression { GIT_BRANCH == 'origin/deve' }
		          }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "preprod" --limit preprod playbook_phonebook.yml'
                   }
               }
               stage("Check that you can connect (GET) to a page and it returns a status 200") {
                   when {
                      expression { GIT_BRANCH == 'origin/deve' }
                  }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "testApi" --limit preprod playbook_phonebook.yml'
                   }
               }
			   stage("Test of the performance jmeter") {
			        when {
					   expression { GIT_BRANCH == 'origin/deve' }
			        }
				   steps {
				       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --limit preprod playbook_jmeter.yml'
				   }
			   }
               stage("Deploy app in production") {
                    when {
                       expression { GIT_BRANCH == 'origin/master' }
                    }
                   steps {
                       sh 'ansible-playbook  -i hosts --vault-password-file vault.key --private-key id_rsa --tags "deploy" --limit prod playbook_phonebook.yml'
                   }
               }
            }
        }
		stage('Find xss vulnerability') {
		    agent { docker {
			    image 'gauntlt/gauntlt'
				    args '--entrypoint='
				}	}
			steps {
			    sh 'gauntlt --version'
				sh 'gauntlt attack/xss.attack'
			}
		}
    }
    post {
    always {
	    script { 
		clean
		slackNotifier currentBuild.result
		}
      }  
    }
}
