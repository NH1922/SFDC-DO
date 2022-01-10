#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    // def HUB_ORG=env.HUB_ORG_DH
    def HUB_ORG = 'nitish_yadav@resilient-shark-kvhinb.com'
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    // def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH
    def CONNECTED_APP_CONSUMER_KEY='3MVG9pRzvMkjMb6k8zybRw_SvhiysewORC8Dmo9cttu.R.1THhuSbvCfEoX1ecOJTv3Laj6V7fC4VlcV09_.h'

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'

    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploye Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
		    //bat "${toolbelt} plugins:install salesforcedx@49.5.0"
		    bat "${toolbelt} update"
            bat "echo \"y\" | ${toolbelt} plugins:install sfdx-git-delta -f"
		    //bat "${toolbelt} auth:logout -u ${HUB_ORG} -p" 
                 rc = bat returnStatus: true, script: "${toolbelt} auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --loglevel DEBUG --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
		
            if (rc != 0) { 
		    println 'inside rc 0'
		    error 'hub org authorization failed' 
	    }
		else{
			println 'rc not 0'
		}

			println rc
			
			// need to pull out assigned username
			if (isUnix()) {
				//rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
				rmsg = sh returnStdout: true, script: "${toolbelt} force:source:deploy -x manifest/package.xml -u ${HUB_ORG}"
			}else{
				// rmsg = bat returnStdout: true, script: "${toolbelt} force:source:deploy -x manifest/package.xml -u ${HUB_ORG}"
			   //rmsg = bat returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
            //    rmsg = bat returnStdout: true, script : "${toolbelt} force:source:convert -d mdapioutput/"
            //    rmsg = bat returnStdout: true, script : "${toolbelt} force:mdapi:deploy -u ${HUB_ORG} -d mdapioutput/ --checkonly --wait -1"
                println('generating file')
                rmsg = bat returnStdout: true, script : "${toolbelt} sgd:source:delta --to \"HEAD\" --from \"HEAD^\" --output \".\""
                printf(rmsg)
                println('package generated')
                bat "type package/package.xml"
                rmsg = bat returnStdout: true, script : "${toolbelt} force:source:deploy -x package/package.xml"
                println('After deployment')
                printf(rmsg)




            }
			  
            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
        }

        // stage('Run Tests'){
        //     if(isUnix()){
        //         // rTestMsg = sh returnStdout: true, script: "${toolbelt} force:apex:test:run"
        //     }else{
        //         bat "${toolbelt} update"
        //         rc = bat returnStatus: true, script: "${toolbelt} auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --loglevel DEBUG --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
        //         if(rc == 0){
        //             rTestMsg = bat returnStatus: true, script: "${toolbelt} force:apex:test:run -u ${HUB_ORG}"
        //         }
        //     }
        // }
    }
}
