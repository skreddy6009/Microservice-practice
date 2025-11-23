pipeline {
    agent any

    environment {
        SONAR_SERVER = 'sonar-server'
        DOCKER_IMAGE = "yourdockerhub/adservice"
        DOCKER_TAG   = "v1"
    }

    stages {
        stage('Prepare Gradle Wrapper') {
            steps {
                sh '''
                    chmod +x gradlew
                '''
            }
        }

        stage('Download Dependencies (downloadRepos)') {
            steps {
                sh '''
                    ./gradlew downloadRepos
                '''
            }
        }
        stage('Run Unit Tests') {
            steps {
                sh '''
                    ./gradlew test
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    ./gradlew clean build -x test
                '''
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv("${SONAR_SERVER}") {
                    sh '''
                        ./gradlew sonarqube
                    '''
                }
            }
        }

        stage('Build Distribution (installDist)') {
            steps {
                sh '''
                    ./gradlew installDist
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                '''
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                    docker login -u "$DOCKER_USER" -p "$DOCKER_PASS"
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }

        stage('Health Probe Test') {
            steps {
                sh '''
                    docker run -d --name adservice -p 9555:9555 ${DOCKER_IMAGE}:${DOCKER_TAG}
                    sleep 5
                    docker exec adservice grpc_health_probe -addr=:9555
                    docker rm -f adservice
                '''
            }
        }
    }

    post {
        always {
            junit 'build/test-results/test/*.xml'
        }
    }
}
