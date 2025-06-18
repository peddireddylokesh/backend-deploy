pipeline {
    agent {
        label 'AGENT-1'
    }

    environment {
        project = "expense"
        component = "backend"
        region = "us-east-1"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    parameters {
        string(name: 'version', description: 'Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick the environment')
    }

    stages {
        stage('Setup Environment') {
            steps {
                script {
                    env.APP_VERSION = params.version
                    env.DEPLOY_ENV = params.deploy_to
                    echo "App Version: ${env.APP_VERSION}, Deploying to: ${env.DEPLOY_ENV}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withAWS(region: "${env.region}", credentials: "aws-credentials") {
                        sh """
                            aws eks update-kubeconfig --region ${env.region} --name expense-${env.DEPLOY_ENV}
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${env.APP_VERSION}/g' values-${env.DEPLOY_ENV}.yaml
                            cat values-${env.DEPLOY_ENV}.yaml
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'This will always run'
            deleteDir()
        }
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        unstable {
            echo 'Pipeline is unstable'
        }
    }
}
