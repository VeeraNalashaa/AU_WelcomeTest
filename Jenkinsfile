pipeline {
    agent any

    // Environment Variables
    environment {
        MAJOR = '1'
        MINOR = '0'
        // Local Deployment Path
        LOCAL_DEPLOY_PATH = 'C:\Jen'
    }

    stages {

        // Printing Basic Information
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

        // Build Stages
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

        // Test Stages
        stage('Test') {
            steps {
                echo 'Testing..the workflow...'
            }
        }

        // Deploy to Local UAT
        stage('Deploy to Local UAT') {
            steps {
                echo "Deploying ${BRANCH_NAME} to Local UAT at ${LOCAL_DEPLOY_PATH}/UAT"
                sh """
                    mkdir -p ${LOCAL_DEPLOY_PATH}/UAT
                    cp -r Output/${env.BUILD_NUMBER}/* ${LOCAL_DEPLOY_PATH}/UAT/
                """
            }
        }

        // Deploy to Local Production
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

    // Options
    options {
        // Timeout for pipeline
        timeout(time: 80, unit: 'MINUTES')
        skipDefaultCheckout()
    }

    // Post Actions
    post {
        success {
            echo 'Deployment has been completed!'
        }
        failure {
            echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
        }
        always {
            /* Clean workspace if success */
            // cleanWs()
        }
    }
}
