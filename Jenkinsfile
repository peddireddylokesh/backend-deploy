pipeline {
    agent {
        label 'AGENT-1'
    }

    environment {
        project = "expense"
        component = "backend"
        region = "us-east-1"
        appVersion = ''
        environment = ''
        
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
                    appVersion = params.version
                    environment = params.deploy_to
                    echo "App Version: ${params.version}, Deploying to: ${environment}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withAWS(region: "${env.region}", credentials: "aws-credentials") {
                        sh """
                            aws eks update-kubeconfig --region ${env.region} --name ${env.project}-${env.ENVIRONMENT}
                            kubectl get nodes
                            cd helm
                            helm upgrade --install ${env.project}-${env.component} -n ${env.project} \
                                -f values-${env.ENVIRONMENT}.yaml \
                                --set deployment.imageURL=897729141306.dkr.ecr.${env.region}.amazonaws.com/${env.project}-${env.component}:${env.APP_VERSION} \
                                .
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
