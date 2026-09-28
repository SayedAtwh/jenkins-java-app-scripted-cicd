pipeline {

    agent {
        label 'agent-1'
    }

    tools {
        jdk 'jdk-11'
        maven 'maven'
    }

    environment {
        IMAGE_NAME = "java-app-declartive"
        IMAGE_TAG = "sayedatwhdevops/java-app-declartive"
        IMAGE_VERSION = "${BUILD_NUMBER}"
        CONTAINER_NAME = "java-app-declartive"
    }

    stages {

        stage("build And Test"){
          stages {
            stage("Build Java Application") {
                steps {
                   sh " mvn clean package -DskipTests=true "
                 }
              }

            stage("Test Java Application") {
                steps {
                  sh " mvn test "
                 }
              }            
          }
        }

        stage("Build Docker Image") {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} .'
            }
        }

        stage("Docker Login into DockerHub") {
            steps {
                withCredentials([
                    string(credentialsId: 'DOCKER_USERNAME', variable: 'DOCKER_USERNAME'),
                    string(credentialsId: 'DOCKER_PASSWORD', variable: 'DOCKER_PASSWORD')
                ]) {
                  
                  sh " docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD} "
                 
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                sh 'docker tag ${IMAGE_NAME}:${IMAGE_VERSION} ${IMAGE_TAG}:${IMAGE_VERSION}'
                sh 'docker push ${IMAGE_TAG}:${IMAGE_VERSION}'
            }
        }

       stage('Deploy') {
         steps {
           sh '''
               docker rm -f ${CONTAINER_NAME} || true

               docker run -d \
                --name ${CONTAINER_NAME} \
                -p 8089:8090 \
                 ${IMAGE_TAG}:${IMAGE_VERSION}
        '''
    }
}
    }
}