pipeline {
          agent any 
  	  environment {
        	DISABLE_AUTH = 'true'
        	DB_ENGINE    = 'sqlite'
       		 // This can be nexus3 or nexus2
        	NEXUS_VERSION = "nexus3"
        	// This can be http or https
        	NEXUS_PROTOCOL = "http"
        	// Where your Nexus is running
    		NEXUS_URL ="13.52.247.123:8081"
        	// Repository where we will upload the artifact
      		NEXUS_REPOSITORY = "test-repo"
        	// Jenkins credential id to authenticate to Nexus OSS
        	NEXUS_CREDENTIAL_ID = "nexus-credentials"
        
       		 JAVA_HOME = "/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.242.b08-0.50.amzn1.x86_64/"
        	//PATH = "${JAVA_HOME}/bin:$PATH"
        	MAVEN_HOME = "/opt/apache-maven-3.6.3"
        	PATH = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin:$PATH"



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
  					// dir("maven-basic"){
       					 withSonarQubeEnv('sonarqube') {
           				 sh "${scannerHome}/bin/sonar-scanner"
       					 }
       					 timeout(time: 10, unit: 'MINUTES') {
           				 waitForQualityGate abortPipeline: true
       					 }
   					// }
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
		 

                 stage('Deploy to Nexus')
		 {
		     steps{






		script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: NEXUS_REPOSITORY,
                            credentialsId: NEXUS_CREDENTIAL_ID,
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }




          }
      
      }






}
}
