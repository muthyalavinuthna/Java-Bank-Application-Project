pipeline {
    agent any
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
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis'){ 
            steps { 
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar' 
                } 
            } 
        } 
        stage('Quality Gate') {
            steps { 
                timeout(time: 2, unit: 'MINUTES') { 
                    waitForQualityGate abortPipeline: true 
                }
            }
        }
        stage('Artifact in Nexus') { 
            steps { 
                withMaven(globalMavenSettingsConfig: 'settings.xml', jdk: 'jdk17', maven: 'maven', traceability: true) { 
                    sh 'mvn deploy' 
                } 
            } 
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sriramyaganni/bank-webapp:latest .'
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push sriramyaganni/bank-webapp:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f bank_deployment.yml'
            }
        }
    }
}
