pipeline {
    agent any

    environment {
        IMAGE = 'balabhadra12/gitops-demo'
    }

    stages {
        stage('Docker-Build') {
            steps {
                sh 'docker build -t ${IMAGE}:${BUILD_NUMBER} app/'
            }
        }

        stage('Docker-Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                        echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${IMAGE}:${BUILD_NUMBER}
                        docker logout
                    '''
                }
            }
        }
    }
}