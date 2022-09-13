pipeline {
  agent any

  environment {
    REPO = "isaac-chia/android-ci"
  }

  stages {
    stage('Run Tests') {
      steps {
          echo 'Running Tests'
          script {
              sh "./gradlew clean assembleRelease testRelease"
          }
      }
    }

    stage('Release') {
      steps {
        script {
          VERSION = sh (
              script: './gradlew -q printVersion',
              returnStdout: true
            ).trim()
          echo "VersionInfo: ${VERSION}"
          echo "TOKEN: ${TOKEN}"

          withCredentials([string(credentialsId: 'github_token ', variable: 'TOKEN')]) {
            sh '''#!/bin/bash

              DATA='{
                  "tag_name": "'$VERSION'",
                  "target_commitish": "main",
                  "name": "'$VERSION'",
                  "body": "Publish '$VERSION'",
                  "draft": false,
                  "prerelease": false
              }'
              curl -H 'Authorization: token $TOKEN'  --data "$DATA" "https://api.github.com/repos/$REPO/releases"
              '''
          }
        }
      }
    }
  }
}
