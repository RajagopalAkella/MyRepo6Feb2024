#!groovy
import groovy.json.JsonSlurperClassic
node {

    // def BUILD_NUMBER=env.BUILD_NUMBER
    // def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    // def SFDC_USERNAME

    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'sfdx'
    println 'loaded toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
		println 'Source checkout done'
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
		println 'Entered into with cred'
        stage('Deploy Code') {
			println 'Entered into Deploy code'
            if (isUnix()) {
				println 'Entered if'
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
				println 'Entered else'
                rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'hub org authorization failed' }

			println 'Printing rc'
			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				println 'pulling assigned username'
				rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
			}else{
				println 'unable to pull'
			   //rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d force-app/. -u ${HUB_ORG}"
               rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy -p force-app -u ${HUB_ORG}"
			}
			
			println 'printing rmsg'
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }
    }
}
