pipeline {
	agent {
		label "jenkins-agent"
	}
	
	environment {
		APP_NAME = "omni-app"
	}
	
	stages{
		stage("Cleanup Workspace"){
			steps{
				cleanWs()
			}
		}
		
		stage("Checkout from SCM"){
			steps{
				git branch: 'main', credentialsId: 'github', url: 'https://github.com/Farah1398/gitops-OmniApp.git'
			}
		}
		
		
		stage("Update the Deployment Tags"){
			steps{
				sh """
					cat deployment.yaml
					sed -i "s/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g" deployment.yaml
					cat deployment.yaml
				"""
			}
		}
		
		stage("Push the changed deployment file to Git"){
			steps{
				
				sh """
					git config --global user.name "farah1398"
					git config --global user.email "farah.ayari@esprit.tn"
					git add deployment.yaml
					git commit -m "Updated Deployment Manifest"
				"""
				
				withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
					sh "git push https://github.com/Farah1398/gitops-OmniApp.git main"
				}
				
			}
		}

	} 	
    post {
        always { 
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: 'UNKNOWN'
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
        
                def body = """
                    <html>
                    <body>
                    <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                    <h2>${jobName} - Build ${buildNumber}</h2>
                    <div style="background-color: ${bannerColor}; padding: 10px;">
                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                    </div>
                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                    </div>
                    </body>
                    </html> 
                """
        
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}", 
                    body: body,
                    to: 'farahayari7262@gmail.com', 
                    from: 'jenkins@example.com', 
                    replyTo: 'jenkins@example.com', 
                    mimeType: 'text/html'
                )
            }
        }
    }

		
}
