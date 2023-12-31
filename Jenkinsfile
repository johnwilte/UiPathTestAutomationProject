pipeline {
 agent any

    	    // Environment Variables
	        environment {
	        MAJOR = '1'
	        MINOR = '0'
	        //Orchestrator Services
	        UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	        UIPATH_ORCH_LOGICAL_NAME = "personaluitesttraining"
	        UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	        UIPATH_ORCH_FOLDER_NAME = "TestFolder"
			}
	

	    stages {
	

	        // Printing Basic Information
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranhName ${env.BRANCH_NAME}"
	                checkout scm
	            }
	        }

	         // Build Stages
	        stage('Build') {
	            steps {
	                echo "Building.. with ${WORKSPACE}"
	                UiPathPack (
                      outputPath: "Output\\${env.BUILD_NUMBER}",
                      projectJsonPath: "project.json",
                      version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
					  //version: AutoVersion(),
                      useOrchestrator: false,
					  traceLevel: 'None'
					//  runWorkflowAnalysis: true
					)
	            }
	        }
	         // Test Stages
	        stage('Test') {
	            steps {
	                echo 'Testing.. the workflow...'
	            }
	        }
	         // Deploy Stages
	        stage('Deploy to UAT') {
	            steps {
	                echo "Deploying ${BRANCH_NAME} to UAT "
					UiPathDeploy (
						createProcess: true,
						packagePath: "Output\\${env.BUILD_NUMBER}",
						orchestratorAddress: "${UIPATH_ORCH_URL}",
						orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
						folderName: "${UIPATH_ORCH_FOLDER_NAME}",
						environments: 'DEV',
						//credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: 'APIUserKey']
						credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
						traceLevel: 'None',
						entryPointPaths: 'Main.xaml'
					)
				}
			}
	
	         // Deploy to Production Step
	        stage('Deploy to Production') {
	            steps {
	                echo 'Deploy to Production'
	                }
	            }
				
			// Test Run
	         stage('Test Run') {
	           steps {
					echo 'Running Test'
					UiPathTest (
					  testTarget: [$class: 'TestSetEntry', testSet: "Test Set for Hands On"],
					  orchestratorAddress: "https://cloud.uipath.com/",
					  orchestratorTenant: "DefaultTenant",
					  folderName: "TestFolder",
					  timeout: 10000,
					  traceLevel: 'None',
					  testResultsOutputPath: "result.xml",
					  //credentials: [$class: 'UserPassAuthenticationEntry', credentialsId: "8DEv1AMNXczW3y4U15LL3jYf62jK93n5"],
					  credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'), 
					  parametersFilePath: ''
					)
	                }
	            }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
	        always {
	            /* Clean workspace if success */
	            cleanWs()
	        }
	    }
	

	}