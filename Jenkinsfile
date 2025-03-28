#!/usr/bin/env groovy

def deploy(servers, branch) {
    for (item in servers) {
        println "Deploying to ${item} on branch ${branch}."
        sh(script: """
        ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ec2-user@'${item}' bash -c "'
            ./jwp-dev.sh
        '"
        """)
    }
}

pipeline {
    agent {
        node {
            label 'docker-node'
        }
    }
    stages {
        stage ('Checkout') {
            steps {
                checkout scm: [
                    $class: 'GitSCM',
                    branches: scm.branches,
                    doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                    extensions: [[$class: 'CloneOption', noTags: false, shallow: false, depth: 0, reference: '']],
                    userRemoteConfigs: scm.userRemoteConfigs
                ]
            }
        }
        stage ('Deploy to dev') {
            when {
                branch 'dev'
            }
            steps {
                script {
                        def servers = ['0.0.0.0']
                        def branch = 'dev'
                        deploy (servers,branch)
                    }
                }
			}
        stage ('Deploy to staging ') {
            when {
                branch 'staging'
            }
            steps {
                script {
                        def servers = ['0.0.0.0']
                        def branch = 'staging'
                        deploy (servers,branch)
                    }
                }
			}
        stage ('deploy to prod') {
            when {
                branch 'main'
            }
            steps {
                script {
                        def servers = ['10.217.131.23']
			def branch = 'main'
                        deploy (servers,branch)
                }
            }
		}
	}
    post { 
        always { 
            echo 'I will always run!'
            office365ConnectorSend status: currentBuild.currentResult, webhookUrl: 'https://tuliptechcom.webhook.office.com/webhookb2/03416099-2273-4106-add3-48f502ff8caf@982780f8-0424-4e57-9cc0-bee3d6acc797/IncomingWebhook/0fc9cfa93f8044f9a0ef1f3c0c04eb08/ed61559d-de88-4909-ac89-30e98d6df87a/V2ic9QZMmLIIMGmC3Th5HVG0y7m70450CFrx9P_-JkBlc1'
        }
    }
}
