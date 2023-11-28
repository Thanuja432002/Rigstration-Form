pipeline
{
	agent any
	environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
	tools
	{
	maven "maven"
	}
	stages{
		stage('Code Checkout'){
			steps{
				git branch: 'main', url: 'https://github.com/Thanuja432002/Rigstration-Form.git'

			}
		}
       stage("SonarQube analysis") {
            steps {
              withSonarQubeEnv('codequality') {
                sh 'mvn clean install sonar:sonar'
              }
            }
          }

        
        
        stage('Maven Plugins Install'){
		steps{
				sh 'mvn install'
			}
				}

		stage('Code Verify by Maven'){
			steps{
				sh 'mvn verify'
			}
		}

		 stage('Maven Clode Validate'){
			steps{
				sh 'mvn validate'
			}
		}

		 stage('Code Test by Maven'){
			steps{
				sh 'mvn test'
			}
		}

		 stage('Code Compile by Maven'){
			steps{
				sh 'mvn compile'
			}
		}

		 stage('Cleaning the Old Artifact'){
			steps{
				sh 'mvn clean'
			}
		}

        stage('War Build'){
			steps{
				sh 'mvn package'
			}
		}

		stage('Deploy War to Nexus'){
			steps{
			sh 'mvn deploy' 
		}
	}
	
	    stage ('DEV Deploy to Tomcat')  {
	        steps{
        echo "deploying to DEV Env "
        deploy adapters: [tomcat9(credentialsId: 'war-deploy-to-tomcat', path: '', url: 'http://34.207.66.154:8080')], contextPath: null, war: '**/*.war'
        }
        }
	stage('Copying Packge'){
			steps{
				sh 'cp /root/.jenkins/workspace/nexus-tomcat-docker-project/webapp/target/webapp.war /root/.jenkins/workspace/nexus-tomcat-docker-project/'
			}
		}
	stage('Build docker image') {
            steps {  
                sh 'docker build -t thanujareddy/registration-form:$BUILD_NUMBER .'
            }
        }
        
        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push image') {
            steps{
                sh 'docker push thanujareddy/registration-form:$BUILD_NUMBER'
            }
        }
		
	}
post {
        always {
            sh 'docker logout'
        }
    }
}

