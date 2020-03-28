pipeline {
          agent any 
  	  environment {
        	DISABLE_AUTH = 'true'
        	DB_ENGINE    = 'sqlite'
   	 }
        stages {
		stage ('Initialize') {
           		 steps {
                  		sh '''
                    		printenv
				export MAVEN_HOME=/opt/apache-maven-3.6.3
                    		export PATH=${MAVEN_HOME}/bin:${PATH}
                    		echo "PATH = ${PATH}"
                    		echo "MAVEN_HOME = ${MAVEN_HOME}"
				export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk.x86_64
				export PATH=${JAVA_HOME}/bin:$PATH
				echo "JAVA_HOME=${JAVA_HOME}"
				mvn -version                  
                		'''
            			}
        	}
		stage('Unit Test')
		{
		    steps 
		    {

                        sh '''
				export MAVEN_HOME=/opt/apache-maven-3.6.3
                                export PATH=${MAVEN_HOME}/bin:${PATH}
				cd maven-basic
			        mvn test

			'''


		    }

		}

                      stage('Sonarqube') {
				    environment {
       					 scannerHome = tool 'SonarQube Scanner'
  					  }
   				     steps {
 					sh ' pwd '	
  					 dir("maven-basic"){
                                         sh ' pwd '  
       					 withSonarQubeEnv('sonarqube') {
           				 sh "${scannerHome}/bin/sonar-scanner"
       					 }
       					 timeout(time: 10, unit: 'MINUTES') {
           				 waitForQualityGate abortPipeline: true
       					 }
   					 }
					}
			} 


        	stage ('Build') {
            		steps {
                		dir ("${env.WORKSPACE}") {
                    		// sh '/opt/apache-maven-3.5.4/bin/mvn -Dmaven.test.failure.ignore=true install' 
					sh '''
					export MAVEN_HOME=/opt/apache-maven-3.6.3
                    		        export PATH=${MAVEN_HOME}/bin:${PATH}
        				mvn clean             		
					mvn -Dmaven.test.failure.ignore=true install
					'''
                		}
            		}
         	}
         //	stage ('Run') {
           // 		steps {       
             //     		dir ("${env.WORKSPACE}/my-app") {
               //     		 sh ' java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App '
                 //		 }
            	//	}      
        	 //}

}
}
