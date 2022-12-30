pipeline{
 
   agent any
   environment {

       VERSION = "${env.BUILD_ID}"
   }

   stages{

       stage('sonar quality check'){

            agent{

                docker {
                    image 'maven'
                }
             }
             steps{

                 script{
                     
                     withSonarQubeEnv(credentialsId: 'sonar-token') {
                      
                       sh'mvn clean package sonar:sonar'
                       }

                 }   
             } 
        }

        stage('Quality Gate status'){

            steps{

                script{

                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }

        stage('docker build & docker push to nexus repo'){

            steps{

                script{
                    withCredentials([string(credentialsId: 'nexus-passwd', variable: 'nexus-creds')]) {
                    sh '''
                    docker build -t 13.126.93.237:8083/springapp:${VERSION} .

                    docker login -u admin -p $nexus-creds 13.126.93.237:8083

                    docker push 13.126.93.237:8083/springapp:${VERSION}

                    docker rmi 13.126.93.237:8083/springapp:${VERSION}
                    '''
                    }
                }
            }

        }
    }
 }
