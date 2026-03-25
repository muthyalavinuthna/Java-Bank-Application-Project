pipeline {
agent any

```
environment {
    DOCKERHUB_USER = "lakshmivinuthnamutyala"
    IMAGE_NAME     = "banking-application"
    IMAGE_TAG      = "${BUILD_NUMBER}"
    DOCKER_CREDS   = "docker-credentials"
    EMAIL_TO       = "lakshmivinuthna0915@gmail.com"
}

stages {

    stage('Checkout Code') {
        steps {
            script {
                try {
                    git branch: 'main',
                    credentialsId: 'Github-Cred',
                    url: 'https://github.com/muthyalavinuthna/Java-Bank-Application-Project.git'

                    emailext (
                        subject: "SUCCESS: Checkout Stage - Build #${BUILD_NUMBER}",
                        body: """<h3>Checkout Completed Successfully</h3>
                                 <p>Job: ${JOB_NAME}</p>
                                 <p>Build: ${BUILD_NUMBER}</p>""",
                        to: "${EMAIL_TO}"
                    )
                } catch (Exception e) {
                    emailext (
                        subject: "FAILED: Checkout Stage - Build #${BUILD_NUMBER}",
                        body: "<h3>Checkout Failed</h3>",
                        to: "${EMAIL_TO}"
                    )
                    throw e
                }
            }
        }
    }

    stage('Build Docker Image') {
        steps {
            script {
                try {
                    sh """
                    docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                    docker tag ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """

                    emailext (
                        subject: "SUCCESS: Docker Build - Build #${BUILD_NUMBER}",
                        body: "<h3>Docker Image Built Successfully</h3>",
                        to: "${EMAIL_TO}"
                    )
                } catch (Exception e) {
                    emailext (
                        subject: "FAILED: Docker Build - Build #${BUILD_NUMBER}",
                        body: "<h3>Docker Build Failed</h3>",
                        to: "${EMAIL_TO}"
                    )
                    throw e
                }
            }
        }
    }

    stage('DockerHub Login') {
        steps {
            script {
                try {
                    withCredentials([usernamePassword(
                        credentialsId: "${DOCKER_CREDS}",
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        sh """
                        echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                        """
                    }

                    emailext (
                        subject: "SUCCESS: Docker Login - Build #${BUILD_NUMBER}",
                        body: "<h3>DockerHub Login Successful</h3>",
                        to: "${EMAIL_TO}"
                    )
                } catch (Exception e) {
                    emailext (
                        subject: "FAILED: Docker Login - Build #${BUILD_NUMBER}",
                        body: "<h3>DockerHub Login Failed</h3>",
                        to: "${EMAIL_TO}"
                    )
                    throw e
                }
            }
        }
    }

    stage('Push Image to DockerHub') {
        steps {
            script {
                try {
                    sh """
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """

                    emailext (
                        subject: "SUCCESS: Docker Push - Build #${BUILD_NUMBER}",
                        body: "<h3>Docker Image Pushed Successfully</h3>",
                        to: "${EMAIL_TO}"
                    )
                } catch (Exception e) {
                    emailext (
                        subject: "FAILED: Docker Push - Build #${BUILD_NUMBER}",
                        body: "<h3>Docker Push Failed</h3>",
                        to: "${EMAIL_TO}"
                    )
                    throw e
                }
            }
        }
    }
}

post {
    success {
        emailext (
            subject: "FINAL SUCCESS: Job ${JOB_NAME} Build #${BUILD_NUMBER}",
            body: "<h2>Pipeline Completed Successfully</h2>",
            to: "${EMAIL_TO}"
        )
    }
    failure {
        emailext (
            subject: "FINAL FAILURE: Job ${JOB_NAME} Build #${BUILD_NUMBER}",
            body: "<h2>Pipeline Failed</h2>",
            to: "${EMAIL_TO}"
        )
    }
}
```

}
