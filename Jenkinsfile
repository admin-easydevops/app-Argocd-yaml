pipeline {
    agent { label 'Jenkins-agent'}
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "camille94"
        DOCKER_PASS = 'dockerhub-PAT'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"

    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
               steps {
                   git branch: 'feature/jenkins', credentialsId: 'github-PAT', url: 'https://github.com/admin-easydevops/app-Argocd-yaml.git'
               }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
                   cat deployment.yaml
                   sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                   cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                   git config --global user.name "admin-easydevops"
                   git config --global user.email "cnsidze@easydevopsconsulting.com"
                   git add deployment.yaml
                   git commit -m "Updated Deployment Manifest"
                """
                withCredentials([gitUsernamePassword(credentialsId: 'github-PAT', gitToolName: 'Default')]) {
                  sh "git push https://github.com/admin-easydevops/app-Argocd-yaml.git origin feature/jenkins"
                }
            }
        }
      
    }
}