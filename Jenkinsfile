pipeline {
    agent any

    tools {
        nodejs 'node-20'
    }

    environment {
        DOCKER_IMAGE_TAG = ""
        CURRENT_STAGE = ''
        NAME = "retrosquad/polytechniquedefi"
        VERSION = "${env.BUILD_ID}"
        DOCKERHUB_USERNAME = 'retrosquad'
        DOCKERHUB_PASSWORD = 'nuitdinfo2023'

    }


    stages {

        stage('Checkout') {
            steps {
                // Specify the Git repository URL
                script {
                    def gitUrl = 'https://github.com/dhakersghaier/NuitDinfo'
                    checkout([$class: 'GitSCM', branches: [[name: '*/main']], userRemoteConfigs: [[url: 'https://github.com/dhakersghaier/NuitDinfo']]])
                }
            }
    }
        stage('Build') {
            steps {
                script {
                    CURRENT_STAGE = 'Build'
                    try {
                        sh "docker build -t ${NAME} ."
                        sh "docker tag ${NAME}:latest ${NAME}:${VERSION}"
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Build failed: ${e.message}")
                    }
                }
            }
        }


        stage('Push to Docker Hub') {
            steps {
                script {
                    CURRENT_STAGE = 'Push to Docker Hub'
                    try {
                        
                            sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                            sh "docker push ${NAME}:${VERSION}"
    
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Push to Docker Hub failed: ${e.message}")
                    }
                }
            }
        }
       
    }    
    post {
        failure {
            script {
                def failedStageName = CURRENT_STAGE ?: "Unknown"
                emailext subject: "Pipeline Failed in Stage: ${currentBuild.fullDisplayName}",
                          body: "The pipeline '${currentBuild.fullDisplayName}' has failed in the '${failedStageName}' stage. Please investigate the issue.",
                          to: "dhakersg@gmail.com",
                          mimeType: 'text/html'
            }
        }
    }
}



