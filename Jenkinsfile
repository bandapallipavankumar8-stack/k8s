pipeline {
    agent any

    environment {
        // Change to your actual S3 bucket name
        S3_BUCKET = 's3://YOUR_BUCKET_NAME/packages'
        // ap-south-1 is the official AWS region code for Mumbai
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
                // Uses the Jenkins build number variable to dynamically name the package
                sh "zip -r package-${BUILD_NUMBER}.zip . -x '*.git*' 'Jenkinsfile' 'README.md' '.gitignore'"
            }
        }

        stage('Upload to S3') {
            steps {
                // Uploads the uniquely named file to your Mumbai S3 bucket
                sh "aws s3 cp package-${BUILD_NUMBER}.zip ${env.S3_BUCKET}/package-${BUILD_NUMBER}.zip --region ${env.AWS_REGION}"
            }
        }
    }

    post {
        always {
            // Clean up workspace memory to prevent disk fill-ups
            sh "rm -f package-${BUILD_NUMBER}.zip"
        }
    }
}
