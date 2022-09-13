pipeline {
  agent any

  environment {
    REPO = "isaac-chia/android-ci"
    VERSION = ""
  }

  stages {
    stage('Run Tests') {
      steps {
          echo 'Running Tests'
          script {
              sh "./gradlew assembleRelease testRelease"
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


          withCredentials([string(credentialsId: 'github_token ', variable: 'TOKEN')]) {
            sh '''#!/bin/bash

              echo "VERSION:$VERSION"
              DATA='{
                  "tag_name": "$VERSION",
                  "target_commitish": "main",
                  "name": "$VERSION",
                  "body": "Publish $VERSION",
                  "draft": false,
                  "prerelease": false
              }'
              RES=$(curl -H "Authorization: token $TOKEN"  --data "$DATA" "https://api.github.com/repos/$REPO/releases")

              ARTIFACT=build/outputs/apk/release/app-release.apk

              upload=$(echo $RES | grep upload_url)
              upload=$(echo $upload | cut -d """ -f4 | cut -d "{" -f1)
              upload="$upload?name=$ARTIFACT"
              uploadResponse=$(curl -H "Authorization: token $TOKEN" \
                   -H "Content-Type: $(file -b --mime-type $ARTIFACT)" \
                   --data-binary @$ARTIFACT $upload)
              '''
          }
        }
      }
    }
  }
}
