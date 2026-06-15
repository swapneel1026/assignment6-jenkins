@Library('ansible_mongo_install') _
def config
pipeline {
    agent any

    stages {

        stage('Load Configuration') {
            steps {
                script {
                    config = loadConfig()
                }
            }
        }
stage('Debug Config') {
    steps {
        script {
            echo "CONFIG = ${config}"
            echo "GIT = ${config.GIT}"
            echo "URL = ${config.GIT?.URL}"
            echo "BRANCH = ${config.GIT?.BRANCH}"
            echo "ANSIBLE = ${config.ANSIBLE}"
        }
    }
}
        stage('Checkout Code') {
            steps {
                script {
                    checkoutCode(config)
                }
            }
        }

        stage('Approval') {
            when {
                expression {
                    return config.KEEP_APPROVAL_STAGE
                }
            }
            steps {
                script {
                    approvalGate(config.ACTION_MESSAGE)
                }
            }
        }

        stage('Deploy MongoDB') {
            steps {
                script {
                    runPlaybook(config)
                }
            }
        }
    }

    post {
        success {
            script {
                notifySlack(
                    "SUCCESS",
                    config.ACTION_MESSAGE
                )
            }
        }

        failure {
            script {
                notifySlack(
                    "FAILED",
                    config.ACTION_MESSAGE
                )
            }
        }
    }
}