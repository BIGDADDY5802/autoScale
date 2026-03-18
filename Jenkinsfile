pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
    }

    stages {

        stage('Verify AWS Identity') {
            steps {
                sh '''
                    echo "Confirming instance profile credentials..."
                    aws sts get-caller-identity
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/BIGDADDY5802/autoScale'
            }
        }

        stage('Initialize Terraform') {
            steps {
                sh 'terraform init -input=false'
            }
        }

        stage('Plan Terraform') {
            steps {
                sh 'terraform plan -input=false -out=tfplan'
            }
        }

        stage('Apply Terraform') {
            steps {
                input message: "Approve Terraform Apply?", ok: "Deploy"
                sh 'terraform apply -input=false -auto-approve tfplan'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful.'
        }
        failure {
            echo 'Deployment failed. Check stage output above.'
            sh 'find . -name "*.tfplan" -delete 2>/dev/null || true'
        }
    }
}