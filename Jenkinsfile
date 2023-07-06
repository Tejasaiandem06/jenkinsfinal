pipeline {
    agent any

    environment {
        function_name = 'jenkinsfinal'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                sh 'mvn package'
            }
        }

        stage("SonarQube analysis") {
            agent any
            when {
                anyOf {
                    branch 'feature/*'
                    branch 'main'
                }
            }
            steps {
                withSonarQubeEnv('Sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    try {
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    } catch (Exception ex) {
                        // Handle exception
                    }
                }
            }
        }

        stage('Push') {
            steps {
                echo 'Push'
                //sh "aws s3 cp target/sample-1.0.3.jar s3://jenkinsfinal"
            }
        }

        stage('Deployments') {
            parallel {
                stage('Deploy to Dev') {
                    steps {
                        echo 'Build'
                        //sh "aws lambda update-function-code --function-name $jenkinsfinal1 --region us-east-1 --s3-bucket jenkinsfinal --s3-key sample-1.0.3.jar"
                    }
                }

                stage('Deploy to test') {
                    when {
                        branch 'main'
                    }
                    steps {
                        echo 'Build'
                        //sh "aws lambda update-function-code --function-name $jenkinsfinal2 --region us-east-1 --s3-bucket jenkinsfinal --s3-key sample-1.0.3.jar"
                    }
                }
            }
        }

        stage('Deploy to Prod') {
            when {
                branch 'main'
            }
            steps {
                input(message: 'Are we good for Prod Deployment ?')
            }
        }

        stage('Release to Prod') {
            when {
                branch 'main'
            }
            steps {
                //sh "aws lambda update-function-code --function-name $jenkinsfinal3 --region us-east-1 --s3-bucket jenkinsfinal --s3-key sample-1.0.3.jar"
            }
        }
    }

    post {
        always {
            echo "${env.BUILD_ID}"
            echo "${BRANCH_NAME}"
            echo "${BUILD_NUMBER}"
        }

        failure {
            echo 'failed'
        }
        aborted {
            echo 'aborted'
        }
    }
}
