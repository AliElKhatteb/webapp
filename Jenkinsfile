pipeline {
	agent any
    tools { 
        maven 'mvn' 
    }
    stages {
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
		    export M2_HOME=/usr/share/maven
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
	     stage ('Check-Git-Secrets') {
		    steps {
	        sh 'rm trufflehog || true'
		sh 'docker pull gesellix/trufflehog'
		sh 'docker run -t gesellix/trufflehog --json https://github.com/AliElKhatteb/webapp.git > trufflehog'
		sh 'cat trufflehog'
	    }
	    }
	    
	  stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
	stage ('Deploy') {
            steps {
                sh 'cp target/*.war /opt/tomcat/webapps'
            }
        }
	stage('Dependency Checker') {
          steps {
              catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                sh 'mvn org.owasp:dependency-check-maven:check'
              }
            
          }
          post {
            always {
              archiveArtifacts allowEmptyArchive: true, artifacts: 'target/dependency-check-report.html', fingerprint: true, onlyIfSuccessful: true
            }
          }
        }
	
       stage('Image Hardening') {
          steps {
              sh 'dockle mysql'
          }
	    
	 
	       
	    
	
    }
}
