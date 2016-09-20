/**
properties([
  parameters([
    string(defaultValue: '1.0', description: 'Current version number', name: 'VERSION'),
    text(defaultValue: '', description: 'A list of changes', name: 'CHANGES'),
    booleanParam(defaultValue: false, description: 'If build should be marked as pre-release', name: 'PRERELEASE'),
    string(defaultValue: 'ayufan-pine64', description: 'GitHub username or organization', name: 'GITHUB_USER'),
    string(defaultValue: 'android-5.1', description: 'GitHub repository', name: 'GITHUB_REPO'),
  ])
])
*/

node('linux && lab-worker && pine64') {
  ws('/opt/pine64/android') {
    timestamps {
      wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
        stage 'Prepare'
        sh 'rm -f *.gz'

        withEnv([
          "VERSION=$VERSION",
          'TARGET=tulip_chiphd-userdebug',
          'USE_CCACHE=true'
        ]) {
          stage 'Regular'
          retry(2) {
            sh '''#!/bin/bash
              source build/envsetup.sh
              lunch "${TARGET}"
              make -j
            '''
          }

          stage 'Image Regular'
          sh '''#!/bin/bash
            source build/envsetup.sh
            lunch "${TARGET}"
            set -xe
            sdcard_image "${JOB_NAME}-v${VERSION}-r${BUILD_NUMBER}.img.gz"
          '''
        }

        withEnv([
          "VERSION=$VERSION",
          'TARGET=tulip_chiphd_atv-userdebug',
          'USE_CCACHE=true'
        ]) {
          stage 'TV'
          retry(2) {
            sh '''#!/bin/bash
              source build/envsetup.sh
              lunch "${TARGET}"
              make -j
            '''
          }

          stage 'Image TV'
          sh '''#!/bin/bash
            source build/envsetup.sh
            lunch "${TARGET}"
            set -xe
            sdcard_image "${JOB_NAME}-tv-v${VERSION}-r${BUILD_NUMBER}.img.gz"
          '''
        }

        withEnv([
          "VERSION=$VERSION",
          "CHANGES=$CHANGES",
          "PRERELEASE=$PRERELEASE",
          "GITHUB_USER=$GITHUB_USER",
          "GITHUB_REPO=$GITHUB_REPO"
        ]) {
          stage 'Release'
          sh '''#!/bin/bash
            set -xe
            shopt -s nullglob

            echo "Hello World" > test

            export PATH=$(pwd)/bin/linux/amd64:$PATH
            if ! which github-release; then
              curl -L https://github.com/aktau/github-release/releases/download/v0.6.2/linux-amd64-github-release.tar.bz2 | tar jx
            fi

            github-release release \
                --tag "${VERSION}" \
                --name "$VERSION: $BUILD_TAG" \
                --description "${CHANGES}\n\n${BUILD_URL}" \
                --draft

            for file in *.gz; do
              github-release upload \
                  --tag "${VERSION}" \
                  --name "$(basename "$file")" \
                  --file "$file"
            done

            if [[ "$PRERELEASE" == "true" ]]; then
              github-release edit \
                --tag "${VERSION}" \
                --name "$VERSION: $BUILD_TAG" \
                --description "${CHANGES}\n\n${BUILD_URL}" \
                --pre-release
            else
              github-release edit \
                --tag "${VERSION}" \
                --name "$VERSION: $BUILD_TAG" \
                --description "${CHANGES}\n\n${BUILD_URL}"
            fi
          '''
        }
      }
    }
  }
}
