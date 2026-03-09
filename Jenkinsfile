pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Gayathrijayaprakash2001/devflow'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t simple-devops-app .'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker rm -f simple-devops-container || exit 0'
                bat 'docker run -d --name simple-devops-container -p 5000:5000 simple-devops-app'
            }
        }
    }
}