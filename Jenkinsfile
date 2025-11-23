pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "arjundocker92/microservice"
        DOCKER_TAG   = "adservice"
        LANGUAGE = "java"
        DOCKER_CREDS_ID = "docker-creds"
        SONAR_PROJECT_KEY = "adservice"
        TRIVY_REPORTS = "adservice"
    }
    stages {
       /* stage ('sonar analysis') {
          steps {
              script {
                  def SONAR_SCANNER_HOME = tool name: 'sonar-scanner'
                  withSonarQubeEnv('sonar') {
                      sh "${SONAR_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=${SONAR_PROJECT_KEY}"
                  }
              }
          }
        }*/
        stage ('Trivy FS Scan') {
            steps {
                script {
                    sh "trivy fs --format json --output ${TRIVY_REPORTS}_reports.json ."
                    archiveArtifacts artifacts: "${TRIVY_REPORTS}_reports.json", fingerprint: true
                }
            }
        }
        stage ('Docker Build') {
            steps {
                script {
                 def time_stamp = sh(script: "date '+%Y-%m-%d_%H-%M-%S'", returnStdout: true).trim()
                  env.TIME_STAMP = time_stamp
                  sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP} ."
                }
            }
        }
        stage ('Trivy Image Scan') {
          steps {
                script {
                    sh "trivy image --format json  --output ${TRIVY_REPORTS}_image_reports.json ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP}"
                    archiveArtifacts artifacts: '${TRIVY_REPORTS}_image_reports.json', fingerprint: true
                }
          }
        }          
        stage ('Docker Image Pushing') {
            steps {
                script {
           withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDS_ID}", usernameVariable: 'USER_NAME', passwordVariable: 'PASSWORD')]) {
            sh """
                echo "$PASSWORD" | docker login -u "$USER_NAME" --password-stdin
            """
            
          sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP}"
        }
      }
     }
    }
   stage ('Clean up') {
    steps {
        script {
            sh """
               docker rmi ${DOCKER_IMAGE}:${DOCKER_TAG}-${env.TIME_STAMP} || true
              docker image prune -f || true
           """
            cleanWs()
          }
       }
   }
       
 }         
}
