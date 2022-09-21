#!/usr/bin/env groovy
node() {

    // parameters {
    //     string(name: 'string', defaultValue: 'sample string', description: 'sample string description')
    //     text(name: 'text', defaultValue: '', description: 'sample text description')
    //     booleanParam(name: 'boolean', defaultValue: true, description: 'sample boolean description')
    //     choice(name: 'choice', choices: ['One', 'Two', 'Three'], description: 'sample choice description')
    //     password(name: 'password', defaultValue: 'sample password', description: 'sample password description')
    //     activeChoiceParam('CHOICE-1') {
    //         description('Allows user choose from multiple choices')
    //         filterable()
    //         choiceType('SINGLE_SELECT')
    //         groovyScript {
    //             script('["choice1", "choice2"]')
    //             fallbackScript('"fallback choice"')
    //         }
    //     }
    // }

    // parameters {

    //     string(defaultValue: "dev", description: 'set env example: dev,uat', name: 'env')
  
    // }

	properties([
	  parameters([
	    string(name: 'submodule', defaultValue: ''),
        choice(choices: 'NonYes', , name: 'choice2'),
        choice(choices: 'NonYes', name: 'choice3')
        // activeChoiceParam('CHOICE-1') {
        //     description('Allows user choose from multiple choices')
        //     filterable()
        //     choiceType('SINGLE_SELECT')
        //     groovyScript {
        //         script('["choice1", "choice2"]')
        //         fallbackScript('"fallback choice"')
        //     }
        // }
	  ])
	])


// [
//     $class: 'DynamicReferenceParameter',
//     choiceType: 'ET_FORMATTED_HIDDEN_HTML',
//     name: 'BranchName',
//     omitValueField: true,
//     script: [
//         $class: 'GroovyScript',
//         fallbackScript: [
//             classpath: [],
//             sandbox: true,
//             script: '''
//                 return '<p>error</p>'
//             '''
//         ], 
//         script: [
//             classpath: [],
//             sandbox: true,
//             script: """
//                 return '<input name="value" value="${env.BRANCH_NAME}" type="text">'
//             """
//         ]
//     ]
// ]
	// properties([[$class: 'ParametersDefinitionProperty', parameterDefinitions: [[$class: 'StringParameterDefinition', name: 'myparam', defaultValue: 'default value']]]])
	// echo "received ${binding.hasVariable('myparam') ? myparam : 'undefined'}"

	try {
		stage('Stage 01'){
			echo "Stage 01"
		}

		stage('Stage 02') {
			echo "Running ${env.BUILD_ID} | ${env.BUILD_TAG} on ${env.JENKINS_URL} | ${currentBuild.number}"
			sh 'printenv'
			echo "env: ${env}"
			echo "submodule: ${submodule}"
			sh 'IP=\$(curl ifconfig.me)'
			sh 'echo "IP:\$IP"'

	        // sh """
	        //     IP=\$(curl ifconfig.me)
	        //     echo "IP:\$IP"
	        // """

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
