pipeline {
    agent any
 environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id') // AWS credentials
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
    }
  stages {
        stage('GIT Checkout') {
            steps {
                git 'https://github.com/your-repo/java-project.git' // Replace with your repo URL
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'mvn clean install'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }
        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                script {
                    sh 
                        scp -i /path/to/your-ssh-key.pem target/your-app.jar ec2-user@your-ec2-instance-public-dns:/path/to/deploy/
                        ssh -i /path/to/your-ssh-key.pem ec2-user@your-ec2-instance-public-dns 'sudo systemctl restart your-app-service'
                }
            }
        }
    }
  post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
        }
        success {
            echo 'Build, test, and deploy succeeded!'
        }
        failure {
            echo 'Build, test, or deploy failed.'
        }
    }
}
