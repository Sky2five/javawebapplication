pipeline{
    agent any
    
    stages{
        stage("Git Checkout"){
            steps{
                git credentialsId: 'github', url: 'https://github.com/Sky2five/javawebapplication'
            }
        }
	/*
        stage('Quality Gate Status Check'){
            steps{
                script{
			withSonarQubeEnv(credentialsId: 'sonar-token') { 
                        // Get Home Path of Maven 
                        def mvnHome = tool name: 'maven-3', type: 'maven'
			sh "${mvnHome}/bin/mvn clean sonar:sonar"
                       	  }
			            timeout(time: 20, unit: 'SECONDS') {
			            def qg = waitForQualityGate()
				                if (qg.status != 'OK') {
					                 error "Pipeline aborted due to quality gate failure: ${qg.status}"
				                }
                          }
                }
            }  
        }
	*/
        stage("Maven Build"){
            steps{
                script{
                // Get Home Path of Maven 
                def mvnHome = tool name: 'my-maven', type: 'maven'
                sh "${mvnHome}/bin/mvn clean package"
                }
            }
        }
	
	/*
	stage("Upload War To Nexus"){
	    steps{
		script{
		    def mavenPom = readMavenPom file: 'pom.xml'
		    def nexusRepoName = mavenPom.version.endsWith("SNAPSHOT") ? "javawebapplication-snapshot" : "javawebapplication-release"
		    nexusArtifactUploader artifacts: [
			[
			    artifactId: 'javawebapplication', 
		            classifier: '', 
			    file: "target/javawebapplication-${mavenPom.version}.war", 
			    type: 'war'
			]
			], 
			    credentialsId: 'nexus3', 
			    groupId: 'in.javahome', 
			    nexusUrl: '172.31.31.37:8081', 
			    nexusVersion: 'nexus3', 
			    protocol: 'http', 
			    repository: nexusRepoName, 
			    version: "${mavenPom.version}"    
                       }
		}
	}
	*/
	    
        stage("Deploy to Tomcat Server"){
            steps{
                sshagent(['aws-keypair']) {
                sh """
		    echo $WORKSPACE
		    mv target/*.war target/jeev.war
                    scp -o StrictHostKeyChecking=no target/jeev.war  ec2-user@172.31.19.72:/opt/apache-tomcat-9.0.65/bin/webapps/
                    ssh ec2-user@172.31.19.72 /opt/apache-tomcat-9.0.65/bin/shutdown.sh
                    ssh ec2-user@172.31.19.72 /opt/apache-tomcat-9.0.65/bin/bin/startup.sh
                
                """
                }
            
            }
        }
    }  
}
    //post {
	    //always {
	    //echo 'Deleting the Workspace'
	    //deleteDir() /* Clean Up our Workspace */
	    //}
	    //success {
		//mail to: 'devopsawsfreetier@gmail.com',
		     //subject: "Success Build Pipeline: ${currentBuild.fullDisplayName}",
		     //body: "The pipeline ${env.BUILD_URL} completed successfully"
	    //}
	    //failure {
		//mail to: 'devopsawsfreetier@gmail.com',
		     //subject: "Failed Build Pipeline: ${currentBuild.fullDisplayName}",
		     //body: "Something is wrong with ${env.BUILD_URL}"
	    //}
    //}
