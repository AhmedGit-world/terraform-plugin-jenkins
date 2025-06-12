pipeline {
    agent any // Since you're using the master, 'any' is fine, or 'master' if you want to be explicit.
    
    environment {
        // Define environment variables for your cloud provider credentials.
        // It's crucial to store these securely in Jenkins Credentials and reference them here.
        // For AWS (example):
        AWS_ACCESS_KEY_ID = credentials('aws-terraform-credentials')
        AWS_SECRET_ACCESS_KEY = credentials('aws-terraform-credentials')
        // For Azure (example):
        // AZURE_CLIENT_ID = credentials('your-azure-client-id-credential-id')
        // AZURE_CLIENT_SECRET = credentials('your-azure-client-secret-credential-id')
        // AZURE_SUBSCRIPTION_ID = credentials('your-azure-subscription-id-credential-id')
        // AZURE_TENANT_ID = credentials('your-azure-tenant-id-credential-id')
    }

    tools {
        // Reference the Terraform installation you configured in Global Tool Configuration
        // Use the Name you gave it (e.g., "Terraform_Manual_Install")
        terraform 'Terraform_Manual_Install' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Terraform Init') {
            steps {
                // Change directory to where your .tf files are located.
                // If they are in the root of your repo, you can remove 'dir()'.
                // If they are in a 'terraform' subdirectory, keep 'dir('terraform')'.
                script {
                    def terraformDir = "terraform" // Adjust this if your .tf files are elsewhere
                    if (fileExists(terraformDir)) {
                        dir(terraformDir) {
                            sh 'terraform init'
                        }
                    } else {
                        // Assume .tf files are in the root if no specific directory
                        sh 'terraform init'
                    }
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                script {
                    def terraformDir = "terraform" // Adjust this
                    if (fileExists(terraformDir)) {
                        dir(terraformDir) {
                            sh 'terraform plan -out=tfplan' 
                        }
                    } else {
                        sh 'terraform plan -out=tfplan'
                    }
                }
            }
        }
        
        // --- Manual Approval Stage REMOVED ---
        // The 'Manual Approval for Apply' stage has been removed as per your request.
        // Terraform Apply will now run automatically after Plan.

        stage('Terraform Apply') {
            steps {
                script {
                    def terraformDir = "terraform" // Adjust this
                    if (fileExists(terraformDir)) {
                        dir(terraformDir) {
                            // Using -auto-approve to bypass confirmation.
                            // Be cautious with this in production environments!
                            sh 'terraform apply -auto-approve tfplan' 
                        }
                    } else {
                        sh 'terraform apply -auto-approve tfplan'
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
        success {
            echo 'Terraform pipeline completed successfully!'
        }
        failure {
            echo 'Terraform pipeline failed!'
        }
    }
}
