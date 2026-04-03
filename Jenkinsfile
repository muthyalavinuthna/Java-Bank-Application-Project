pipeline {
    agent any

    tools {
        maven 'maven3'
        jdk 'jdk21'
    }

    environment {
        DOCKER_IMAGE = "lakshmivinuthnamutyala/banking-application:${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-creds',
                    url: 'https://github.com/muthyalavinuthna/Java-Bank-Application-Project.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis'){ 
            steps { 
                withSonarQubeEnv('sq') {
                    sh 'mvn sonar:sonar'
                } 
            } 
        } 

        stage('Quality Gate') {
            steps { 
                timeout(time: 5, unit: 'MINUTES') { 
                    waitForQualityGate abortPipeline: true 
                }
            }
        }

        stage('Deploy Artifact to Nexus') { 
            steps { 
                withMaven(
                    maven: 'maven3',
                    jdk: 'jdk17',
                    globalMavenSettingsConfig: 'settings.xml'
                ) { 
                    sh 'mvn deploy -DskipTests'
                } 
            } 
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f Deployment.yml'
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully ✅"
        }
        failure {
            echo "Pipeline failed ❌"
        }
    }
}
