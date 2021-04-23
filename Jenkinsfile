pipeline {
        agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: hybris
    image: signet/hybris-ant:1811_21-V8
    command: 
    - /bin/bash
    tty: true	
  - name: maven
    image: maven:3.6.1-slim
    command: 
    - cat
    tty: true
'''
            label 'sample-java-app'
            idleMinutes 10
            defaultContainer 'jnlp'
        }
    }  
    
    environment {
	
	//Tokens
	sap_token = credentials('sap_token')
	ccv2_sub = credentials('ccv2_sub')

	}

		
    	stages {
        
		stage('Build') {
            		steps {
			
				container('hybris') {
					
					script{
						propfile = readProperties(file: './project.properties_PROD')	
					}
			
                    			sh '''
						echo "Performing  build..."
                        			
		                    	'''
		    			
		    				   
                 		} 
            		}   
        	}
		
		stage('Unit Test') {
            		steps {
				container('hybris') {
				
					sh '''
					
						echo "Running JUnit..."
						

						'''
			
					
			
                 		} 
            		}   
        	}
		
		stage('Code Quality') {
			when { expression {propfile['code_quality'] == "true" }}
            		steps {
				
                    				sh ''' 
						
						echo "SOnar"
						
						'''
				
            		}
        	}
	
		stage('Create Temp Branch') {
	
			when { expression { GIT_BRANCH == 'dev' || propfile['build_code'] == "true" } }
			steps {
			
				
				echo "Temp branch"
				
			}
		}
	    
		stage('CCV2_Build') {
			when { expression {GIT_BRANCH == 'dev' || propfile['build_code'] == "true" }}
            		steps {
				container('hybris') {
					
					echo "I am executing Build "
					
					
					}
				}
				}

		stage('CCV2_Deploy') {
			when { expression {GIT_BRANCH == 'dev' ||  (propfile['auto_deploy'] == "true" && ((GIT_BRANCH).startsWith('project') )) ||  (propfile['auto_deploy'] == "true" && ((GIT_BRANCH).startsWith('release') )) }}
            		steps {
				container('hybris') {
					script{
						propfile = readProperties(file: './project.properties_PROD')
						if (GIT_BRANCH == 'dev') {
							ccv2_database_strategy=propfile['ccv2_database_strategy']
							ccv2_database_update_mode=propfile['ccv2_database_update_mode']
							ccv2_env_code=propfile['ccv2_env_code']
						}
						else {
							ccv2_database_strategy=propfile['ccv2_auto_database_strategy']
							ccv2_database_update_mode=propfile['ccv2_auto_database_update_mode']
							ccv2_env_code=propfile['ccv2_auto_env_code']
							
						}
 
						
					}
				 
					sh'''
				
						echo "propfile['ccv2_auto_database_strategy']"
						echo "$ccv2_database_update_mode"
						echo "$ccv2_env_code"
      
          					echo " Deploying now"
          				
					'''
					
					
					
				}
            		}
        	}

		stage('Post Deploy Tests') {
			when { expression {env.GIT_BRANCH == 'dev' ||  (propfile['auto_post_deploy_tests'] == "true" && ((GIT_BRANCH).startsWith('project') )) ||  (propfile['auto_post_deploy_tests'] == "true" && ((GIT_BRANCH).startsWith('release') )) }}
			parallel {
				stage('Functional Test') {
				when {expression {ondemand_functionaltest == "true" }}
					steps {
						script {
							echo "I am executing Smoke Test on target dev environment post deployment"
				
							
						}
					
				}
				}
				stage('Security Test') {
					when {expression {ondemand_securitytest == "true" }}
					steps {
						echo 'I am running Security Test here'
					}
				}
				stage('Performance Test') {
					when {expression {ondemand_perfomancetest == "true" }}
					steps {
						echo 'I am running Performance Test here'
					}
				}
			}
		}    
  	}
  	post {
	  
		always {
			script {
				if (propfile['javadoc'] == "true") {
					javadoc(javadocDir: "/$WORKSPACE", keepAll: true)
        			}
		  	}
	  	}
	  
        	failure {
			script {
			
			echo "Failed"
				
			}
		}
		success {
			script {
				
			echo "Passed"
			}
		}
   	}
}
