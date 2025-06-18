pipeline {
    agent {
        label 'AGENT-1'
    }
    environment {
        project = "expense"
        component = "backend"
        deploy_To = "production"
        region = "us-east-1"
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        // retry(1)
    }
    parameters {
        string(name: 'version', description: 'enter the application version')
        
    }
    stages {                               
        stage('Deploy') {

            steps {
                script{
                    withAWS(region: 'us-east-1', credentials: "aws-credentials") {
                        sh """
                            aws eks update-kubeconfig --region $region --name expense-dev
                            kubectl get nodes
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