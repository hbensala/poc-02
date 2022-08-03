#!/usr/bin/env groovy

node(any) {
  	wrap([$class: 'TimestamperBuildWrapper']) {

		try {
			stage('Stage 01'){
				echo "Stage 01"
			}

			stage('Checkout') {
				echo "Running ${env.BUILD_ID} | ${env.BUILD_TAG} on ${env.JENKINS_URL} | ${currentBuild.number}"
				sh 'printenv'
				
				checkout scm

				def props

				// --- Retrieve module list from settings.gradle file
				props = readFile file: 'some.settings'
				props.readLines().each {
					if (it.startsWith('include')) {
						modules << it.split('\'')[1]
					}
				}
				echo "Modules: ${modules}"

				// --- Populate versionMap (module versions)
				modules.each {
					props = readProperties file: it+'/version.properties'
					versionMap[it] = props.version
				}
				echo "VersionMap: ${versionMap}"

			}

		} catch(err) {
			echo err.getMessage()
			//This will run only if failed'
			echo 'This will run only if failed'
			throw err
		} finally {
			echo "we're done"
		}
	}
}
