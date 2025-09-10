pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
        S3_BUCKET          = 'webhosting2369'   // replace with actual bucket
        CLOUDFRONT_ID      = 'E31F4CDZ0T3RS2'        // replace with your distribution ID
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/syam-ch/syamo326.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to S3') {
            steps {
                withAWS(credentials: 'bb96ee64-1a03-4d34-af85-dd1b44ecadc5', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws s3 sync build/ s3://$S3_BUCKET --delete
                    '''
                }
            }
        }

        stage('Invalidate CloudFront Cache') {
            steps {
                withAWS(credentials: 'bb96ee64-1a03-4d34-af85-dd1b44ecadc5', region: "${AWS_DEFAULT_REGION}") {
                    sh '''
                        aws cloudfront create-invalidation \
                        --distribution-id $CLOUDFRONT_ID \
                        --paths "/*"
                    '''
                }
            }
        }
    }
}
