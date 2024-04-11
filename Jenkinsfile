CODE_CHANGES = getGitChanges()
def gv 

pipeline {
    agent any //where to execute 

    environment { 
	NEW_VERSION = '1.0'
	SERVER_CREDENTIALS = credentials('<ID_OF_CRED>')
    }  //Define ENV

    tools {
	maven "<name of installation>"
	gradle "<name of installation>"
	jdk "<name of installation>"
    }

    parameters {
	string {name: 'VERSION', defaultValue: '',description: 'prod's version'}
	choice {name: 'VERSION', choices: ['1.0','1.1','1.2'], description:''}
	booleanParam {name: 'executeTests', defaultValue: True, description:''}
    }

    stages {
	stage('init') {
	   steps {
		script {
		   gv = load "script.groovy"
		 }
	    }
	}

        stage('build') {
	    when {
		 expression {
			BRANCH_NAME =='main' ||  BRANCH_NAME =='dev'
			BRANCH_NAME =='main' && CODE_CHANGES == true
			params.executeTests == True
		 }
	    }

            steps {
                echo "BUILD VERSION ${NEW_VERSION}"
		echo "deploying version ${params.VERSION}"
		script {
		    gv.deploApp()
		}
	     }
	  }

        stage('test') {
            steps {
                echo 'TEST STAGE'
//Alternative to environment definition. Assign username to USERNAME variable and same for password to PWD.
		withCredentials ([
		     usernamePassword(credentials: '<ID_OF_CRED>', usernameVariable: USERNAME, passwordVariable: PWD)
		])  {
		     sh "some script ${USERNAME} ${PWD}"
		}

             }
	  }

        stage('deploy') {
	    input {
		message "Select the environment to deploy to"
		ok "Done"
		parameters {
			choice {name: 'ENV-ONE', choices: ['staging','prod','dev'], description: ''}
			choice {name: 'ENV-TWO', choices: ['staging','prod','dev'], description: ''}    
//ENV parameter only available in this stage but you can make it gloabally accessible 
			   }	
		     }		
            steps {
                echo "DEPLOYING WITH ${SERVER_CREDENTIALS} TO ${ENV-ONE}"
		script {
		    gv.deployApp()
		}
             }
         }

    }
}


post {
    always {
	//No matter if the build failed or succedded
           }

    success {
	//
           }

    failure {
	//
           }
}
