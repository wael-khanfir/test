pipeline{
    agent any
    tools {
        maven 'M2_HOME'
    }


    stages {


 stage('preparation : start sonar, nexus, local mysql') {
            steps{
                	sh "docker start bfa"
                	sh "docker start 188"
                	sh "docker start 8a8"
            }
        }


        stage('Getting project from Git') {
            steps{
      			checkout([$class: 'GitSCM', branches: [[name: '*/aziz']],
			extensions: [],
			userRemoteConfigs: [[url: 'https://github.com/med-aziz-ben-haha/cicdback.git']]])
            }
        }



        stage('Cleaning the project') {
            steps{
                	sh "mvn -B -DskipTests clean  "
            }
        }



        stage('Artifact Construction') {
            steps{
                	sh "mvn -B -DskipTests package "
            }
        }



         stage('Unit Tests') {
            steps{
               		 sh "mvn test "
            }
        }



        stage('Code Quality Check via SonarQube') {
            steps{

             		sh "  mvn sonar:sonar -Dsonar.projectKey=cicdback -Dsonar.host.url=http://172.10.0.140:9000 -Dsonar.login=6274f40d3b7e537ee22128230d4682d39ffc9542"

            }
        }


        stage('Publish to Nexus') {
            steps {


  sh 'mvn clean package deploy:deploy-file -DgroupId=com.esprit.examen -DartifactId=tpAchatProject -Dversion=1.2 -DgeneratePom=true -Dpackaging=jar -DrepositoryId=deploymentRepo -Durl=http://localhost:8081/repository/cicdback/ -Dfile=target/tpAchatProject-1.2.jar'


            }
        }

stage('Build Docker Image') {
                      steps {
                          script {
                            sh 'docker build -t azizbenhaha/spring-app:latest .'
                          }
                      }
                  }

                  stage('login dockerhub') {
                                        steps {
                                      sh 'echo dckr_pat_-SnwrdC_ELsL6it2JT6cgIcAlrs | docker login -u azizbenhaha --password-stdin'
                                            }
		  }
	    
	                      stage('Push Docker Image') {
                                        steps {
                                   sh 'docker push azizbenhaha/spring-app:latest'
                                            }
		  }

		    stage('stop local mysql') {
                                                  steps {
                                             sh 'docker stop 188'
                                                      }
          		  }

		   stage('Run Spring && MySQL Containers') {
                                steps {
                                    script {
                                      sh 'docker compose up -d'
                                    }
                                }
                            }

	    



     
}

	    
        post {
		success{
		mail bcc: '', body: '''Dear Med Aziz, 
we are happy to inform you that your pipeline build was successful. 
Great work ! 
-Jenkins Team-''', cc: '', from: 'mohamedaziz.benhaha@esprit.tn', replyTo: '', subject: 'Build Finished - Success', to: 'mohamedaziz.benhaha@esprit.tn'
		}
		
		failure{
mail bcc: '', body: '''Dear Med Aziz, 
we are sorry to inform you that your pipeline build failed. 
Keep working ! 
-Jenkins Team-''', cc: '', from: 'mohamedaziz.benhaha@esprit.tn', replyTo: '', subject: 'Build Finished - Failure', to: 'mohamedaziz.benhaha@esprit.tn'
		}

        always {
		emailext attachLog: true, body: '', subject: 'Build finished',from: 'mohamedaziz.benhaha@esprit.tn' , to: 'mohamedaziz.benhaha@esprit.tn'
            cleanWs()
        }
    }

    
	
}
       
