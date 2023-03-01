#!groovy

import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    def SF_USERNAME=env.SF_USERNAME
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID
    def TEST_LEVEL='RunLocalTests'
    def PACKAGE_NAME='0Ho1U000000CaUzSAK'
    def PACKAGE_VERSION
    def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"
    def DEPLOY_DIR = 'force-app'

    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        checkout scm
    }
    
    withEnv(["HOME=${env.WORKSPACE}"]) {
        
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: 'server_key_file')]) {

            stage('Authorize Sandbox') {
                rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setalias acxdev16"
                if (rc != 0) {
                    error 'Salesforce Sandbox org authorization failed.'
                }
            }

            stage('Push to Sandbox Org and Run Tests') {
                rc = command "${toolbelt}/sfdx force:source:deploy --wait 10 --sourcepath ${DEPLOY_DIR} --targetusername acxdev16 --testlevel ${TEST_LEVEL}"
                if (rc != 0) {
                    error 'Salesforce push to Sandbox and Run Tests.'
                }
            }
        }
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}