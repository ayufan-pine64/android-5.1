properties [
	parameters([
		string(defaultValue: '1.0', description: '', name: 'VERSION'),
		text(defaultValue: '', description: '', name: 'CHANGES')
	])
]

node('linux && lab-worker && pine64') {
	ws('/opt/pine64/android') {
		stage 'Regular'
		retry(4) {
			sh '''#!/bin/bash

			source build/envsetup.sh
			lunch tulip_chiphd-userdebug
			make -j5
			'''
		}

		stage 'TV'
		retry(4) {
			sh '''#!/bin/bash

			source build/envsetup.sh
			lunch tulip_chiphd_atv-userdebug
			make -j5
			'''
		}

		stage 'Image'
		sh '''#!/bin/bash
		source build/envsetup.sh
		lunch tulip_chiphd-userdebug
		sdcard_image "${BUILD_TAG}.img.gz"
		'''

		sh '''#!/bin/bash
		source build/envsetup.sh
		lunch tulip_chiphd_atv-userdebug
		sdcard_image "${BUILD_TAG}.img.gz"
		'''

		stage 'Artifacts'
		archiveArtifacts artifacts: 'jenkins-*.gz', excludes: null, fingerprint: true, onlyIfSuccessful: true
	}
}
