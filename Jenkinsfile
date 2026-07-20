pipeline {

    agent any

    tools {
        jdk 'Java21'
        maven 'Maven3'
    }

    environment {
        DOCKER_IMAGE = 'souravpanda/java-docker-ci-cd-pipeline'
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
                    -t "$DOCKER_IMAGE:$BUILD_NUMBER" \
                    -t "$DOCKER_IMAGE:latest" .
                '''
            }
        }

        stage('Docker Login and Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerHubCred',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin

                        docker push "$DOCKER_IMAGE:$BUILD_NUMBER"
                        docker push "$DOCKER_IMAGE:latest"
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
