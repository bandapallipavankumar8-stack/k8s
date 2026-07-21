pipeline {
    agent any

    environment {
        // Configured with your real bucket name from the screenshot
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
                // Replace 123456789012 and YourAdminRoleName with your actual AWS details
                withEnv(['AWS_ROLE_ARN=arn:aws:iam::123456789012:role/YourAdminRoleName']) {
                    sh """
                    # Step 1: Assume the Admin IAM Role using AWS STS
                    CREDENTIALS=\$(aws sts assume-role --role-arn "${AWS_ROLE_ARN}" --role-session-name "Jenkins-S3-Upload" --region ${AWS_REGION} --query "Credentials.[AccessKeyId,SecretAccessKey,SessionToken]" --output text)
                    
                    # Step 2: Inject the temporary tokens into the environment
                    export AWS_ACCESS_KEY_ID=\$(echo "\$CREDENTIALS" | awk '{print \$1}')
                    export AWS_SECRET_ACCESS_KEY=\$(echo "\$CREDENTIALS" | awk '{print \$2}')
                    export AWS_SESSION_TOKEN=\$(echo "\$CREDENTIALS" | awk '{print \$3}')
                    
                    # Step 3: Copy the package to your 'code-version' bucket
                    aws s3 cp package-${BUILD_NUMBER}.zip ${env.S3_BUCKET}/package-${BUILD_NUMBER}.zip --region ${env.AWS_REGION}
                    """
                }
            }
        }
    }

    post {
        always {
            sh "rm -f package-${BUILD_NUMBER}.zip"
        }
    }
}
