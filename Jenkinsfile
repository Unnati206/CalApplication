pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Unnati206/CalApplication.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    mvn sonar:sonar \
                    -Dsonar.projectKey=java-app \
                    -Dsonar.host.url=http://43.205.116.106:9000 \
                    -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Prepare Deployment') {
            steps {
                sh '''
                mkdir -p /home/ansible/app
                cp target/*.jar /home/ansible/app/CalApplication.jar
                '''
            }
        }

        stage('Deploy Using Ansible') {
            steps {
                sh '''
                cd /home/ansible/ansible
                ansible-playbook -i inventory deploy.yml
                '''
            }
        }

    }

    post {

        success {
            echo 'Application Build & Deployment Successful'
        }

        failure {
            echo 'Pipeline Failed'
        }

        always {
            cleanWs()
        }

    }
}
