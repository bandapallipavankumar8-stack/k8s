pipeline {
    agent any

    environment {
        // Your confirmed bucket name and region
        S3_BUCKET = 's3://code-version/packages'
        AWS_REGION = 'ap-south-1'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Package Code') {
            steps {
                sh "zip -r package-${BUILD_NUMBER}.zip . -x '*.git*' 'Jenkinsfile' 'README.md' '.gitignore'"
            }
        }

        stage('Upload to S3') {
            steps {
                // Uses your EC2's 'Terraform-launch' role credentials automatically
                sh "aws s3 cp package-${BUILD_NUMBER}.zip ${env.S3_BUCKET}/package-${BUILD_NUMBER}.zip --region ${env.AWS_REGION}"
            }
        }
    }

    post {
        always {
            sh "rm -f package-${BUILD_NUMBER}.zip"
        }
    }
}
