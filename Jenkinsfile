pipeline {
    agent any

    environment {
        IMAGE = 'balabhadra12/gitops-demo'
        REPO  = 'github.com/balabhadra-09/gitops-demo.git'
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

        stage('Update-Manifest') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'github-creds',
                    usernameVariable: 'GIT_USER',
                    passwordVariable: 'GIT_TOKEN'
                )]) {
                    sh '''
                        sed -i "s|image: ${IMAGE}:.*|image: ${IMAGE}:${BUILD_NUMBER}|" k8s/deployment.yaml
                        git config user.email "jenkins@local"
                        git config user.name "jenkins"
                        git add k8s/deployment.yaml
                        git commit -m "ci: update image tag to ${BUILD_NUMBER} [skip ci]"
                        git push https://${GIT_USER}:${GIT_TOKEN}@${REPO} HEAD:main
                    '''
                }
            }
        }

        stages {
    stage('Check Skip') {
        steps {
            script {
                def msg = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                if (msg.contains('[skip ci]')) {
                    currentBuild.result = 'NOT_BUILT'
                    error('Skipping: commit from Jenkins')
                }
            }
        }
    }
    stage('Docker-Build') { ... }
    stage('Docker-Push') { ... }
    stage('Update-Manifest') { ... }
}
    }
}