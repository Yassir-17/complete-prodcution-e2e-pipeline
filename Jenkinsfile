pipeline {
    agent any // Use any available agent.

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        SCANNER_HOME = tool 'SonarQube Scanner'
        DOCKER_IMAGE_NAME = "yassir17/${APP_NAME}:${BUILD_NUMBER}"
        
    }

    stages {
        stage("Cleanup Workspace"){
            steps {
                cleanWs() //Buildin function to clean workspace
            }
        }
        
        stage('Git Checkout') {
            steps{
                git branch: 'main', credentialsId: 'Yassir-17-Github', url: 'https://github.com/Yassir-17/complete-prodcution-e2e-pipeline.git'
            }
        }
        
        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application"){
            steps {
                sh "mvn test"
            }

        }
        
        stage('Sonarqube Analysis') {
            environment {
                SONAR_URL = "http://192.168.0.11:9000/"
            }
            steps {
                withSonarQubeEnv('Local SonarQube') {
                   withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                      sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN} -Dsonar.host.url=${SONAR_URL} -Dsonar.projectName=complete-production-e2e-pipeline -Dsonar.projectKey=complete-prodcution-e2e-pipeline"
                    }
                }
            }
        }
        
        /*stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }*/
        
        stage('Build & push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker-hub-cred', toolName: 'docker') {
                        sh "docker build -t ${APP_NAME}:latest ."
                        sh "docker tag ${APP_NAME}:latest ${DOCKER_IMAGE_NAME}"
                        sh "docker push ${DOCKER_IMAGE_NAME}"
                    }
                }
            }
        }
        
        stage ('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${DOCKER_IMAGE_NAME}"
                    sh "docker rmi ${APP_NAME}:latest"
                }
            }
        }
        
        stage('Update Deployment File') {
          environment {
             GIT_REPO_NAME = "complete-prodcution-e2e-pipeline"
             GIT_USER_NAME = "yassir-17"
            }
          steps {
             withCredentials([usernamePassword(credentialsId: 'Yassir-17-Github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                  sh '''
                      git config user.email "anfalbla@gmail.com"
                      git config user.name "yassir-17"
                      BUILD_NUMBER=${BUILD_NUMBER}
                      sed -i "s|\\(yassir17/complete-prod-e2e-pipeline:\\)[^ ]*|\\1${BUILD_NUMBER}|g" deployment/deployment.yaml
                      git add .
                      git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                      git push https://${GIT_USER}:${GIT_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                    '''
                }
            }
        }
    }
}

