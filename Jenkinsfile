pipeline {
    agent any

    triggers {
        pollSCM('* * * * *') // Checks for commits every minute
    }

    environment {
        APP_NAME = "todo-app"
        IMAGE_TAG = "${APP_NAME}:${env.BUILD_ID}"
        REPO_URL = 'https://github.com/JasperSpilliaert/todo-list-app.git'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    
                    sh "docker build -t ${IMAGE_TAG} ."
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
                        sh "docker stop ${APP_NAME} || true"
                        sh "docker rm ${APP_NAME} || true"
                    }
                    sh "docker run -d --name ${APP_NAME} -p 8080:8080 ${IMAGE_TAG}"
                }
            }
        }

        stage('Acceptance Test') {
            steps {
                script {
                    sh 'sleep 5'
                    sh 'curl http://localhost:8080 | grep "Todo List Application"'
                }
            }
        }
    }
}
