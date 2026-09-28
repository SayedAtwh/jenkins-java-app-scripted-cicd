node('agent-1') {

    def jdkHome = tool 'jdk-11'
    def mavenHome = tool 'maven'

    env.JAVA_HOME = jdkHome
    env.PATH = "${mavenHome}/bin:${jdkHome}/bin:${env.PATH}"

    def IMAGE_NAME = "java-app-declartive"
    def IMAGE_TAG = "sayedatwhdevops/java-app-declartive"
    def IMAGE_VERSION = "${BUILD_NUMBER}"
    def CONTAINER_NAME = "java-app-declartive"


    stage("Checkout") {
        git 'https://github.com/SayedAtwh/jenkins-java-app-scripted-cicd.git'

        sh 'ls -la'
        sh 'pwd'
    }


    stage("Build Java Application") {
        sh 'mvn clean package -DskipTests=true'
    }


    stage("Test Java Application") {
        sh 'mvn test'
    }


    stage("Build Docker Image") {
        sh "docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} ."
    }


    stage("Docker Login into DockerHub") {

        withCredentials([
            string(
                credentialsId: 'DOCKER_USERNAME',
                variable: 'DOCKER_USERNAME'
            ),
            string(
                credentialsId: 'DOCKER_PASSWORD',
                variable: 'DOCKER_PASSWORD'
            )
        ]) {

            sh '''
                docker login \
                    -u "$DOCKER_USERNAME" \
                    -p "$DOCKER_PASSWORD"
            '''
        }
    }


    stage("Push Docker Image") {

        sh "docker tag ${IMAGE_NAME}:${IMAGE_VERSION} ${IMAGE_TAG}:${IMAGE_VERSION}"

        sh "docker push ${IMAGE_TAG}:${IMAGE_VERSION}"
    }


    stage("Deploy") {

        sh """
            docker rm -f ${CONTAINER_NAME} || true

            docker run -d \
                --name ${CONTAINER_NAME} \
                -p 8080:8080 \
                ${IMAGE_TAG}:${IMAGE_VERSION}
        """
    }
}