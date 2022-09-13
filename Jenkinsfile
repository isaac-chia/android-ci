pipeline {
  stages {
          stage('Run Tests') {
              steps {
                  echo 'Running Tests'
                  script {
                      sh "./gradlew clean assembleRelease testRelease"
                  }
              }
          }
  }
}
