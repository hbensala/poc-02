#!/usr/bin/env groovy

//////////////////// Branches parameters ////////////////////

def getAllBranches(String url, String defaultBranch = "master") {
	echo "Getting branches for ${url} - ${defaultBranch}"
	def branches = []
	def branches_cmd = "git ls-remote -t -h ${url}"
	def out = new StringBuilder()
    def err = new StringBuilder()
	Process process = branches_cmd.execute();
	process.consumeProcessOutput( out, err )
	process.waitFor()
	if( err.size() > 0 ) {
		echo "Issue getting branches"
		branches = []
	}
	if( out.size() > 0 ) {
		echo "Successfully loading branches"
		branches = out.readLines().collect { it.split()[1].replaceAll("refs/heads/", "") }
	}
	return branches
}

def populateBranches(List<String> branches){
	return choice(choices: branches, name: 'Branch', description: 'Select a branch to build')
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

def populateModules(List<String> modules){
	def options = []
	modules.each {
		def opt = booleanParam(defaultValue: false, name: it, description: '')
		options.add(opt)
	}

	return options;
}


//////////////////// Pipelines ////////////////////

node() {

	try {

		stage('Stage 01'){
			echo "Stage 01: init"
			checkout scm
			def options = []

			def repositoryUrl = scm.userRemoteConfigs[0].url as String
			def branch   = scm.branches[0].name
			echo "Branch: ${branch}"

			// def branches = getAllBranches(repositoryUrl, "release-1.x.x")
			// def branches_options = populateBranches(branches)
			// options.add(branches_options)

			def modules = getAllModules('some.settings')
			def modules_options = populateModules(modules)
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
			echo "env: ${env}"

			checkout scm

			def props
			def modules = []
			def versionMap = [:]
			def _modules = ["module-01"]

			// --- Retrieve module list from settings.gradle file
			props = readFile file: 'some.settings'
			props.readLines().each {
				if (it.startsWith('include')) {
					def m = it.split('\'')[1]
					if (m in _modules) {
					    modules << it.split('\'')[1]
					}
				}
			}
			echo "Modules: ${modules}"

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
