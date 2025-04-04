pipeline {
    agent any

    environment {
        AZURE_CREDENTIALS_ID = 'jenkins-pipeline-asp'
        RESOURCE_GROUP = 'WebServiceRG'
        APP_SERVICE_NAME = 'WebApppppppppppp01'
        TF_WORKING_DIR = '.'  // Ensure this is the correct directory where Terraform files exist
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Abhimanyu2811/WebApiJenkins.git'
            }
        }

        stage('Terraform Init') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat """
                    az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%
                    echo "Checking Terraform Installation..."
                    terraform -v
                    echo "Navigating to Terraform Directory: $TF_WORKING_DIR"
                    cd $TF_WORKING_DIR
                    echo "Initializing Terraform..."
                    terraform init
                    """
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                bat """
                echo "Navigating to Terraform Directory: $TF_WORKING_DIR"
                cd $TF_WORKING_DIR
                echo "Generating Terraform Plan..."
                terraform plan -out=tfplan
                """
            }
        }

        stage('Terraform Apply') {
            steps {
                bat """
                echo "Navigating to Terraform Directory: $TF_WORKING_DIR"
                cd $TF_WORKING_DIR
                echo "Applying Terraform Plan..."
                terraform apply -auto-approve tfplan
                """
            }
        }
    }

    post {
        success {
            echo 'Terraform Execution Successful! 🎉'
        }
        failure {
            echo 'Terraform Execution Failed! ❌'
        }
    }
}
