pipeline {
    agent any

    environment {
        SONAR_SERVER = 'sonar-server'
        DOCKER_IMAGE = "arjundocker92/microservice"
        DOCKER_TAG   = "adservice"
    }

    stages {
        stage {
            steps {
                script {
                 def time_stamp = sh(script: "date '+%Y-%m-%d_%H-%M-%S'", returnStdout: true).trim()
                  env.TIME_STAMP = time_stamp
                  sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP} ."
                }
            }
        }
        stage ('docker image pushing') {
            steps {
                script {
        withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'C', passwordVariable: 'PASSWORD')]){
          echo '{$PASSWORD} | docker login -u {PASSWORD} --pasword-stdin'
          sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP}"
        }
    }
   }
 }
            
 }         
}
