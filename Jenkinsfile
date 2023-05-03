#!groovy

import groovy.json.JsonSlurperClassic

node {

    def BUILD_NAME=env.BUILD_NAME
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST=env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID=env.JWT_KEY_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    //def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY
    //def SF_USERNAME=env.SF_USERNAME
    //def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID
    //def TEST_LEVEL='RunLocalTests'
    //def PACKAGE_NAME='0Ho1U000000CaUzSAK'
    //def PACKAGE_VERSION
    //def SF_INSTANCE_URL = env.SF_INSTANCE_URL ?: "https://login.salesforce.com"

    println 'KEY IS'
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'


    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage('checkout source') {
        //when running in multi-branch job, one must issue this command
        checkout scm
    }


    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if(isUnix()) {
		rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            } else {
		bat "${toolbelt} update"  
                rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) {
		println 'inside rc 0'
		error 'hub org authorization failed'
	    }
	    else {
		println 'rc not 0'
	    }

            println rc

            // need to pull out assigned username
		if (isUnix()) {
                rmsg = sh returnStdout: true, script: "${toolbet} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
            } else {
                rmsg = bat returnStdout: true, script: "\"${toolbet}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
            }

            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
