pipeline {

    agent any

    tools {
        jdk 'Java21'
        maven 'Maven3'
    }

    environment {
        DOCKER_IMAGE = 'souravpanda/java-docker-ci-cd-pipeline'
        IMAGE_TAG = "${1}"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                    -t ${souravpanda/java-docker-ci-cd-pipeline}:${1} \
                    -t ${souravpanda/java-docker-ci-cd-pipeline}:latest .
                '''
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerHubCred',
                        usernameVariable: 'souravpanda',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "souravpanda" \
                        --password-stdin

                        docker push ${souravpanda/java-docker-ci-cd-pipeline}:${1}
                        docker push ${souravpanda/java-docker-ci-cd-pipeline}:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            junit(
                testResults: 'target/surefire-reports/*.xml',
                allowEmptyResults: true
            )
        }

        success {
            echo 'Docker CI/CD Pipeline completed successfully!'
        }

        failure {
            echo 'Docker CI/CD Pipeline failed!'
        }
    }
}
