#!groovy
import groovy.json.JsonSlurperClassic
node {
	
    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME
    def HUB_ORG=env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY=env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY
    def toolbelt = tool 'toolbelt'
    def rc

    stage('Checkout Source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Authorization Org') {
            if (isUnix()) {		
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{	      
                rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\"  --setdefaultusername --instanceurl ${SFDC_HOST}"
	    }
            if (rc != 0) { error 'hub org authorization failed' }
	        println rc		
	 }


      	stage('Validate') {
	  if (isUnix()) {
		rmsg = sh returnStdout: true, script: "${toolbelt} force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
	   }else{
		 bat "\"${toolbelt}\" force:source:convert --rootdir force-app --outputdir tmp_convert"
		 bat "jar -cfM unpackaged.zip tmp_convert"
		 bat "rm -rf tmp_convert"
		 rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -c -f unpackaged.zip -u ${HUB_ORG}"
	  }
	    println(rmsg)
	} 	

        
	/*
        stage('Deploy with Source Command') {	
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
			}else{
			    rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
			}
            println(rmsg)
        } 

        stage('Deploy with MDAPI Command') {	
			if (isUnix()) {
				rmsg = sh returnStdout: true, script: "${toolbelt} force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
			}else{
                rc = bat returnStdout: true, script: "\"${toolbelt}\" sfdx force:source:convert --rootdir force-app --outputdir tmp_convert"
                rc1 = bat returnStdout: true, script: "\"${toolbelt}\" sfdx force:source:convert --rootdir force-app --outputdir tmp_convert"
                rc2 = bat returnStdout: true, script: "\"${toolbelt}\" sfdx force:source:convert --rootdir force-app --outputdir tmp_convert"
                
			    rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
			}
			  
            printf rmsg
            println(rmsg)
        }   
        
        */ 
    }
}
