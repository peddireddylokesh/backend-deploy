pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
        project = "expense"
        component = "backend"
        deploy_To = "production"
        region = "us-east-1"
        appVersion = ''
        environment = ''
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        // retry(1)
    }
    parameters {
        string(name: 'version', description: 'enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa','prod'], description: 'Pick something')
    }
   
    stages {    
        stage('setup Environment') {
            steps {
                script {
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }
            steps {
                script {
                    if (params.DEPLOY_TO == 'dev') {
                        environment = 'dev'
                    } else if (params.DEPLOY_TO == 'qa') {
                        environment = 'qa'
                    } else if (params.DEPLOY_TO == 'prod') {
                        environment = 'prod'
                    }
                    echo "Deploying to ${environment} environment"
                }
            }
        }
        stage('Deploy') {

            steps { 
                script{
                    withAWS(region: 'us-east-1', credentials: "aws-credentials") {
                        sh """
                            aws eks update-kubeconfig --region $region --name expense-dev
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${param.version}/g' values-${environment}.yaml
                            cat values-${environment}.yaml
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
            echo 'This will run only if the pipeline is successful'
        }
        failure {
            echo 'This will run only if the pipeline fails'
        }
        unstable {
            echo 'This will run only if the pipeline is unstable'
        }
    }
}