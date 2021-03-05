pipeline {
    agent any    
    tools {
      maven 'my-maven-3.6.3' 
    }    
    stages {    	 
	    stage ('Clone') {
          steps {
             git branch: 'master', url: "https://gitlab.com/mromdhani/08-jenkins-cicd-pipeline-maven-01-continious-integration.git"
          }
	    }	
	 
	stage('Build & Unit test'){
		  steps {
				sh 'mvn clean verify -DskipITs=true'
		      	junit "**/target/surefire-reports/TEST-*.xml"
		      	archiveArtifacts 'target/*.war'
	      }
   	}
	stage('Static Code Analysis'){
	     	 steps {
    			sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
	         }
	}
	stage ('Integration Test'){
	      steps {
    			sh  'mvn clean verify -Dsurefire.skip=true'
				junit '**/target/failsafe-reports/TEST-*.xml'
      			archiveArtifacts 'target/*.war'
      		}
      			
	}
	 stage ('Publish'){
	     steps {
		      script {
					def server = Artifactory.server('My Default Artifactory Server')
					def rtMaven = Artifactory.newMavenBuild()
					rtMaven.resolver server: server, releaseRepo: 'libs-release', snapshotRepo: 'libs-snapshot'
					rtMaven.deployer server: server, releaseRepo: 'libs-release-local', snapshotRepo: 'libs-snapshot-local'
					rtMaven.tool = 'apache-maven-3.6.3'
					def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'install'
                    server.publishBuildInfo buildInfo
		        }
	      }
	  }
   }
  }
