pipeline {
  
     environment {
       PREFIX_IMAGE = "grethoremas3333"
       IMAGE_NAME = "alpinehelloworld"
       IMAGE_TAG = "latest"
       STAGING = "application-staging"
       PRODUCTION = "application-production"
       CONTAINER_NAME = "containeralpineheloworld"
       PORT_EXPOSED = "4141"
       INTERNAL_PORT = "4141"
       FUSION_IMG = "${PREFIX_IMAGE}/${IMAGE_NAME}:${IMAGE_TAG}"

     }
     agent {label 'docker'}
     stages {
         stage('Build image') {
             agent {label 'docker'}
             steps {
                script {
                  sh 'docker build -t ${PREFIX_IMAGE}/$IMAGE_NAME:$IMAGE_TAG .'
                }
             }
        }
        stage('Run container based on builded image') {
            agent {label 'docker'}
            steps {
               script {
                 sh '''
                    echo "Clean Environment"
                    docker container stop $CONTAINER_NAME || echo "container does not exist"
                    docker container $CONTAINER_NAME || echo "container does not exist"
                    docker run --name $CONTAINER_NAME -d -p ${PORT_EXPOSED}:${INTERNAL_PORT} -e PORT=${INTERNAL_PORT} ${PREFIX_IMAGE}/$IMAGE_NAME:$IMAGE_TAG
                    sleep 5
                 '''
               }
            }
       }
       stage('Test image') {
           agent {label 'docker'}
           steps {
              script {
                sh '''
                    #curl http://172.17.0.1:${PORT_EXPOSED} | grep -q "Hello world!"
                    curl http://172.17.0.1:${PORT_EXPOSED}
                '''
              }
           }
      }
      stage('Clean Container') {
          agent {label 'docker'}
          steps {
             script {
               sh '''
                 docker container stop $CONTAINER_NAME
                 docker container rm $CONTAINER_NAME
               '''
             }
          }
     }         
          
     stage ('Login and Push Image on docker hub') {
          agent {label 'docker'}
          environment {
             DOCKERHUB_PASSWORD  = credentials('identifiant_DockerHub')
        }            
          steps {
             script {
               sh '''
                   echo $DOCKERHUB_PASSWORD_PSW | docker login -u $PREFIX_IMAGE --password-stdin
                   docker push ${PREFIX_IMAGE}/$IMAGE_NAME:$IMAGE_TAG
               '''
             }
          }
      }    
     
     stage('STAGING - Deploy app') {
      agent {label 'docker'}
      steps {
          script {
            sh """
            """
          }
        }
     }



     stage('PRODUCTION - Deploy app') {
       when {
              expression { GIT_BRANCH == 'origin/main' }
            }
      agent {label 'docker'}
      environment {
           PLATEFORM_API_TOKEN  = credentials('plateform.sh_api_key')
        }       
      steps {
          script {
            sh """
            """
          }
        }
     }
  }
     
}
