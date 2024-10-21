pipeline {
    agent any
    environment {
        S3_BUCKET = 'test1.yusufkaya.xyz'
        S3_ARN = 'arn:aws:s3:::test1.yusufkaya.xyz'
    }
    stages {
        stage('Check S3 Bucket for Files') {
            steps {
                script {
                    def fileCount = sh(script: "aws s3 ls s3://${S3_BUCKET} --recursive | wc -l", returnStdout: true).trim()
                    if (fileCount.toInteger() > 0) {
                        echo "S3 bucket is not empty. Proceeding to empty the bucket."
                    } else {
                        echo "S3 bucket is already empty. Proceeding to the next stage."
                    }
                }
            }
        }

        stage('Empty S3 Bucket if Not Empty') {
            when {
                expression {
                    def fileCount = sh(script: "aws s3 ls s3://${S3_BUCKET} --recursive | wc -l", returnStdout: true).trim()
                    return fileCount.toInteger() > 0
                }
            }
            steps {
                script {
                    echo "Emptying the S3 bucket..."
                    sh "aws s3 rm s3://${S3_BUCKET} --recursive"
                }
            }
        }

        stage('Upload Files to S3 Bucket') {
            steps {
                script {
                    echo "Uploading files to S3 bucket..."
                    sh "aws s3 sync . s3://${S3_BUCKET} --exclude 'Jenkinsfile'"
                }
            }
        }

        stage('Set Files Public using ACL') {
            steps {
                script {
                    echo "Making files public using ACL..."
                    sh "aws s3api put-bucket-acl --bucket ${S3_BUCKET} --acl public-read"
                    sh "aws s3api put-object-acl --bucket ${S3_BUCKET} --acl public-read --key 'your-file-key'"
                }
            }
        }

        stage('Output S3 Bucket Website Endpoint') {
            steps {
                script {
                    def websiteUrl = sh(script: "aws s3api get-bucket-website --bucket ${S3_BUCKET} || echo 'Website not configured'", returnStdout: true).trim()
                    echo "S3 Bucket Website Endpoint: ${websiteUrl}"
                }
            }
        }
    }
}
