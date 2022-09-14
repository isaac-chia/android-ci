pipeline {
  agent any

  environment {
    REPO = "isaac-chia/android-ci"
  }

  stages {
    stage('Build') {
      steps {
          echo 'Build'
          script {
              sh "./gradlew assembleRelease testRelease"
          }
      }
    }

    stage('Release') {
      steps {
        script {
          def APP_VERSION = sh (
              script: './gradlew -q printVersion',
              returnStdout: true
            ).trim()
          echo "VersionInfo: ${APP_VERSION}"

          withCredentials([string(credentialsId: 'github_token ', variable: 'TOKEN')]) {
            sh '''#!/bin/bash

              echo "VERSION:'''+ APP_VERSION +'''"
              DATA='{
                  "tag_name": "'''+ APP_VERSION +'''",
                  "target_commitish": "main",
                  "name": "'''+ APP_VERSION +'''",
                  "body": "Publish '''+ APP_VERSION +'''",
                  "draft": false,
                  "prerelease": false
              }'
              RES=$(curl -H "Authorization: token $TOKEN"  --data "$DATA" "https://api.github.com/repos/$REPO/releases")
              ARTIFACT='app/build/outputs/apk/release/app-release-unsigned.apk'
              echo "$RES"

              upload=$(echo "$RES" | grep upload_url)

              if [ -z "$upload" ] ; then
                exit 1
              fi

              upload=$(echo $upload | cut -d '"' -f4 | cut -d "{" -f1)

              upload="$upload?name=app-release.apk"

              uploadResponse=$(curl -H "Authorization: token $TOKEN" -H "Content-Type: $(file -b --mime-type $ARTIFACT)" --data-binary @$ARTIFACT $upload)

              '''
          }
        }
      }
    }
  }
}
