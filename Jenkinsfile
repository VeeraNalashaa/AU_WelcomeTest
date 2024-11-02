pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Local Deployment Path
        LOCAL_DEPLOY_PATH = 'C:\\Jen\\'
    }

    stages {
        stage('Preparing') {
            steps {
                echo "Jenkins Home ${env.JENKINS_HOME}"
                echo "Jenkins URL ${env.JENKINS_URL}"
                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
                echo "Jenkins JOB Name ${env.JOB_NAME}"
                echo "GitHub BranchName ${env.BRANCH_NAME}"
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building..with ${WORKSPACE}"
                UiPathPack (
                    outputPath: "Output\\${env.BUILD_NUMBER}",
                    projectJsonPath: "project.json",
                    version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
                    useOrchestrator: false,
                    traceLevel: 'None'
                )
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..the workflow...'
            }
        }

        stage('Deploy to Local UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to Local UAT at ${LOCAL_DEPLOY_PATH}/UAT"
                sh """
                    mkdir -p ${LOCAL_DEPLOY_PATH}/UAT
                    cp -r Output/${env.BUILD_NUMBER}/* ${LOCAL_DEPLOY_PATH}/UAT/
                """
            }
        }

        stage('Deploy to Local Production') {
            steps {
                echo "Deploying ${BRANCH_NAME} to Local Production at ${LOCAL_DEPLOY_PATH}/Production"
                sh """
                    mkdir -p ${LOCAL_DEPLOY_PATH}/Production
                    cp -r Output/${env.BUILD_NUMBER}/* ${LOCAL_DEPLOY_PATH}/Production/
                """
            }
        }
    }

    options {
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    post {
        success {
            steps {
                echo 'Deployment has been completed!'
            }
        }
        failure {
            steps {
                echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
            }
        }
        always {
            steps {
                // Uncomment the next line if you want to clean the workspace after every run
                // cleanWs()
            }
        }
    }
}
