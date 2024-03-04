pipeline {
    agent any

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select the action to perform')
        choice(name: 'environment', choices: ['dev', 'prod', 'uat'], description: 'Select the environment')
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Madhur5723/jenkinsPipeline.git'
            }
        }
        stage('Terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('Plan') {
            steps {
                sh 'terraform plan -out tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('Apply / Destroy') {
            steps {
                script {
                    def environmentName = "${params.environment}-resourcetype"
                    if (params.action == 'apply') {
                        if (!params.autoApprove) {
                            def plan = readFile 'tfplan.txt'
                            input message: "Do you want to apply the plan?",
                                  parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
                        }

                        sh "terraform ${params.action} -input=false tfplan"
                    } else if (params.action == 'destroy') {
                        sh "terraform ${params.action} --auto-approve"
                    } else {
                        error "Invalid action selected. Please choose either 'apply' or 'destroy'."
                    }

                    if (params.action == 'apply') {
                        if (params.environment == 'dev') {
                            sh "mkdir -p madhurshinde-158619/staticfile"
                            sh "echo 'This is a static file for Dev environment' > madhurshinde-158619/staticfile/staticfile.txt"
                        } else if (params.environment == 'prod') {
                            // Handle prod environment specific actions
                        } else if (params.environment == 'uat') {
                            // Handle uat environment specific actions
                        } else {
                            error "Invalid environment selected. Please choose either 'dev', 'prod', or 'uat'."
                        }
                    }
                }
            }
        }

    }
}
