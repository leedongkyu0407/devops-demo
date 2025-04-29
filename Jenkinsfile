pipeline {
  agent any

  stages {
    stage('Detect changed services') {
      steps {
        script {
          def changedDirs = sh(
            script: '''git diff --name-only HEAD~1 HEAD | awk -F/ '{print $1}' | sort -u''',
            returnStdout: true
          ).trim().split('\n')

          env.CHANGED_SERVICES = changedDirs.findAll {
            it == 'user-service' || it == 'scheduler-service'
          }.join(',')

          echo "Changed services: ${env.CHANGED_SERVICES}"
        }
      }
    }

    stage('Build & Push Docker Images') {
      when {
        expression { return env.CHANGED_SERVICES }
      }
      steps {
        script {
          def services = env.CHANGED_SERVICES.split(',')
          def BUILD_NUMBER = env.BUILD_NUMBER
          def GIT_COMMIT_SHORT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()

          withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            for (svc in services) {
              dir(svc) {
                def imageName = "use02012/${svc}"
                def imageTag = "${GIT_COMMIT_SHORT}-${BUILD_NUMBER}"
                echo "Building and pushing ${imageName}:${imageTag}"

                sh """
                  echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                  docker build -t ${imageName}:${imageTag} -t ${imageName}:latest .
                  docker push ${imageName}:${imageTag}
                  docker push ${imageName}:latest
                """
              }
            }
          }
        }
      }
    }
  }
}