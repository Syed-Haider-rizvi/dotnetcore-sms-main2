pipeline {
    agent any

    environment {
        DOTNET_CLI_HOME = "C:\\Program Files\\dotnet"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Restoring dependencies
                    //bat "cd ${DOTNET_CLI_HOME} && dotnet restore"
                    bat "dotnet restore"

                    // Building the application
                    bat "dotnet build --configuration Release"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Running tests
                    bat "dotnet test --no-restore --configuration Release"
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    // Publishing the application
                    bat "dotnet publish --no-restore --configuration Release --output .\\publish"
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'coreuser2', passwordVariable: 'CREDENTIAL_PASSWORD', usernameVariable: 'CREDENTIAL_USERNAME')]) {
                    powershell '''
                    
                    $credentials = New-Object System.Management.Automation.PSCredential($env:CREDENTIAL_USERNAME, (ConvertTo-SecureString $env:CREDENTIAL_PASSWORD -AsPlainText -Force))

                    
                  # Ensure the path is properly quoted if it contains spaces or special characters
                    $networkPath = "\\172.16.14.79\coreapp2"
                    New-PSDrive -Name X -PSProvider FileSystem -Root $networkPath -Persist


                    
                    Copy-Item -Path '.\\publish\\*' -Destination 'X:\' -Force

                    
                    Remove-PSDrive -Name X
                    '''
                }
                }
            }
        }
    }

    post {
        success {
            echo 'Build, test, and publish successful!'
        }
    }
}
