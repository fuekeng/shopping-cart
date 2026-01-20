pipeline {
    agent any
    
    tools {
        jdk 'jdk21'
        maven 'maven3'
    }
    
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/fuekeng/shopping-cart.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }
        
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        
        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '77397857-6e14-4a26-a19d-9f6ceb5bbfd7', toolName: 'docker') {
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag shopping-cart billsoft237/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '77397857-6e14-4a26-a19d-9f6ceb5bbfd7', toolName: 'docker') {
                        sh "docker push billsoft237/shopping-cart:latest"
                    }
                }
            }
        }
        
        stage('Deploy To Docker Container') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '77397857-6e14-4a26-a19d-9f6ceb5bbfd7', toolName: 'docker') {
                        // Arrêter et supprimer le conteneur existant s'il existe
                        sh """
                            docker stop shopping || true
                            docker rm shopping || true
                        """
                        
                        // Démarrer le nouveau conteneur
                        sh "docker run -d --name shopping -p 8070:8070 billsoft237/shopping-cart:latest"
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo "Deployment successful! Application running at http://localhost:8070"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
