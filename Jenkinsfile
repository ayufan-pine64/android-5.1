node('linux && lab-worker && pine64') {
  ws('/opt/pine64/android') {
    timestamps {
      wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
        sh 'rm -f *.gz'

/*
        withEnv([
          'TARGET=tulip_chiphd_atv-userdebug',
          'USE_CCACHE=true'
        ]) {
          stage 'Build'
          retry(2) {
            sh '''#!/bin/bash
              source build/envsetup.sh
              lunch "${TARGET}"
              make -j6
            '''
          }

          stage 'Image'
          sh '''#!/bin/bash
        		source build/envsetup.sh
        		lunch "${TARGET}"
            set -xe
            rm -f *.img.gz
        		sdcard_image "${BUILD_TAG}.img.gz"
          '''
        }*/

        sh '''#!/bin/bash
          set -xe
          shopt -s nullglob

          export GITHUB_USER=ayufan-pine64
          export GITHUB_REPO=android-5.1

          export PATH=$(pwd)/bin/linux/amd64:$PATH
          if ! which github-release; then
            curl -L https://github.com/aktau/github-release/releases/download/v0.6.2/linux-amd64-github-release.tar.bz2 | tar jx
          fi

          github-release release \
              --tag "${BUILD_TAG:-$VERSION}" \
              --name "${BUILD_TAG:-$VERSION}" \
              --description "${CHANGES}" \
              ${PRERELEASE/1/--pre-release}

          for file do *.gz; do
            github-release release \
                --tag "${BUILD_TAG:-$VERSION}" \
                --name "${BUILD_TAG:-$VERSION}" \
                --file "$file"
          done
        '''

        archiveArtifacts artifacts: 'jenkins-*.gz', excludes: null, fingerprint: true, onlyIfSuccessful: true
      }
    }
  }
}
