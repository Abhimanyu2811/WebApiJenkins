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
                git branch: 'master', url: 'https://github.com/Abhimanyu2811/WebApiJenkins.git'
            }
        }


         stage('Terraform Init') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
                    bat """
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
        withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
            bat """
            echo "Navigating to Terraform Directory: %TF_WORKING_DIR%"
            cd %TF_WORKING_DIR%
            terraform plan -out=tfplan
            """
        }
    }
}


        stage('Terraform Apply') {
    steps {
        withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
            bat """
            echo "Navigating to Terraform Directory: %TF_WORKING_DIR%"
            cd %TF_WORKING_DIR%
            echo "Applying Terraform Plan..."
            terraform apply -auto-approve tfplan
            """
        }
    }
}

    

        stage('Build') {
            steps {
                bat 'dotnet restore'
                bat 'dotnet build --configuration Release'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

       stage('Deploy') {
    steps {
        withCredentials([azureServicePrincipal(credentialsId: AZURE_CREDENTIALS_ID)]) {
            bat """
            az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%
            powershell Compress-Archive -Path ./publish/* -DestinationPath ./publish.zip -Force
            az webapp deploy --resource-group %RESOURCE_GROUP% --name %APP_SERVICE_NAME% --src-path ./publish.zip --type zip
            """
        }
    }
}

    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
