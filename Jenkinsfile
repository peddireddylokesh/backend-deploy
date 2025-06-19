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
                            aws eks update-kubeconfig --region $region --name expense-${environment}
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                            helm upgrade --install $component -n $project -f values-${environment}.yaml .

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
