// pipeline {
//     agent any

//     stages {
//         stage('Checkout') {
//             steps {
//                 git branch: 'main', url: 'https://github.com/Gayathrijayaprakash2001/devflow'
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 bat 'docker build -t simple-devops-app .'
//             }
//         }

//         stage('Run Container') {
//             steps {
//                 bat 'docker rm -f simple-devops-container || exit 0'
//                 bat 'docker run -d --name simple-devops-container -p 5000:5000 simple-devops-app'
//             }
//         }
//     }
// }

pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t simple-devops-app .'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                docker ps -aq --filter "name=simple-devops-container" > container_id.txt
                set /p CONTAINER_ID=<container_id.txt
                if not "%CONTAINER_ID%"=="" docker rm -f simple-devops-container
                del container_id.txt
                '''
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker run -d --name simple-devops-container -p 5000:5000 simple-devops-app'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}