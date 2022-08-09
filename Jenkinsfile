#!/usr/bin/env groovy


//////////////////// Global variables ////////////////////

def allModules = []
def modules = []
def versionMap = [:]
def inputPrefix = "input_"


//////////////////// Pipelines ////////////////////

node() {

	try {


		stage('Setup pipeline'){

			cleanWs()
			echo "Cleaned Up Workspace"

			checkout scm
			echo "Checked Out git"

			def options = []

			def repositoryUrl = scm.userRemoteConfigs[0].url as String
			def branch   = scm.branches[0].name
			committerEmail = sh (
				script: 'git --no-pager show -s --format=\'%ae\'',
				returnStdout: true
			)

			echo "[Context] \
			\nBUILD_ID           = ${env.BUILD_ID} \
			\nBUILD_TAG          = ${env.BUILD_TAG} \
			\nJENKINS_URL        = ${env.JENKINS_URL} \
			\nrepositoryUrl      = ${repositoryUrl} \
			\nbranch             = ${branch} \
			\ncommitterEmail     = ${committerEmail} \
			\ncurrentBuildNumber = ${currentBuild.number} \
			"
		}

		stage('Stage 01'){
			def options = []

			allModules = getAllModules('some.settings')
			def modules_options = populateModules(allModules, inputPrefix)
			options.addAll(modules_options)

			properties([
			  parameters(
			  	options
			  	)
			])
		}

		stage('Stage 02: build') {
			echo "Running ${env.BUILD_ID} | ${env.BUILD_TAG} on ${env.JENKINS_URL} | ${currentBuild.number}"
			sh 'printenv'
			echo ">> env: ${env}"
			echo ">> params: ${params}"

			checkout scm

			def props

			modules = allModules.findAll { params[(inputPrefix+it)] }

			// --- Populate versionMap (module versions)
			modules.each {
				props = readProperties file: it+'/version.properties'
				versionMap[it] = (props.version).replace("x", "${currentBuild.number}")
			}
			echo "VersionMap: ${versionMap}"
		}

	} catch(err) {
		echo err.getMessage()
		//This will run only if failed'
		echo 'This will run only if failed'
		throw err
	} finally {
		echo "we're done 01"
	}
}



//////////////////// Modules parameters ////////////////////

def getAllModules(String path){
	echo "Getting modules from ${path}"
	def modules = []
	raws = readFile file: path
	raws.readLines().each {
		if (it.startsWith('include')) {
			modules << it.split('\'')[1]
		}
	}
	echo "Modules: ${modules}"
	return modules
}

def populateModules(List<String> modules, String prefix = "input_"){
	def options = []
	modules.each {
		def opt = booleanParam(defaultValue: true, name: prefix + it, description: '')
		options.add(opt)
	}

	return options;
}
