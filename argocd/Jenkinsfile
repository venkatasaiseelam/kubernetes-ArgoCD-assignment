pipeline {
    agent {
        label '<AGENT_LABEL>'
    }
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "<IMAGE_NAME>"
        GIT_REPO = "<REPO_URL>"
    }

    stages {
        stage('Code') {
            steps {
                git branch: 'main', url: "${GIT_REPO}"
            }
        }
        stage('Code Q.A.') {
            steps {
                echo 'QA is skipped'
            }
        }
        stage('Build Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:v${IMAGE_TAG} .'
            }
        }
        stage('Trivy') {
            steps {
                echo 'Trivy scan is skipped'
            }
        }
        stage('repository') {
            steps {
                script{
                    withDockerRegistry(credentialsId: '<DOCKER_REGISTRY_CREDENTIALS_ID>') {
                        sh 'docker push ${IMAGE_NAME}:v${IMAGE_TAG}'
                    }
                }
            }
        }
        stage('update deployment file') {
            steps {
                script {
                    withCredentials([gitUsernamePassword(credentialsId: '<CREDENTIALS_ID>', gitToolName: 'Default')]) {
                        dir('<REPO_DIRECTORY>') {
                            sh """
                            yq -i '.spec.template.spec.containers[0].image = "${IMAGE_NAME}:v${IMAGE_TAG}"' <DEPLOYMENT_FILE>
                            """
                            
                            sh """
                            git config user.email "<EMAIL_ADDRESS>"
                            git config user.name "<USERNAME>"
                            
                            git add <DEPLOYMENT_FILE>
                            git commit -m "Updated image to version ${IMAGE_TAG}" || true
                            git push origin main
                            """
                        }
                    }
                }
            }
        }
    }
}
