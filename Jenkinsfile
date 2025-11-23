pipeline {
    agent any

    environment {
        SONAR_SERVER = 'sonar-server'
        DOCKER_IMAGE = "yourdockerhub/adservice"
        DOCKER_TAG   = "v1"
         GRADLE_OPTS = ""
    }

    stages {
        stage('Prepare Gradle Wrapper') {
            steps {
                sh '''
                    java -version
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
        stage('Build Distribution (installDist)') {
            steps {
                sh '''
                    ./gradlew installDist
                '''
            }
        }

    }
}
