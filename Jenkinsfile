pipeline {
  agent any
  tools {
  maven 'maven'
  }
  environment {
    IMAGE = readMavenPom().getArtifactId()
    VERSION = readMavenPom().getVersion()
  }
  stages {
    stage ('git_checkout') {
	  steps {
	   git branch: 'dev', url: 'https://github.com/mohancggrl/hello-world-war.git'
	  }
	}
	stage ('mvn_build') {
	  steps {
	   sh "mvn clean package"
	  }
	}
	stage ('Upload_nexus') {
	  steps {
	    nexusArtifactUploader artifacts: [
		[
		    artifactId: "${IMAGE}", 
		    classifier: '', 
		    file: "target/${IMAGE}-${VERSION}.war", 
		    type: 'war'
		]
	   ], 
	   credentialsId: 'Nexus3', 
	   groupId: 'com.efsavage', 
	   nexusUrl: '172.31.12.143:8081', 
	   nexusVersion: 'nexus3', 
	   protocol: 'http', 
	   repository: 'Hello_World', 
	   version: "${VERSION}"
	  }
	}
	stage ('Deploy') {
	  steps {
	   git 'https://github.com/mohancggrl/CD.git'
	   sshPublisher (publishers: [
		    sshPublisherDesc
			(
			  configName: 'ansbile', 
			  transfers: [
			    sshTransfer
				(
				cleanRemote: false, excludes: '',
				execCommand: "ansible-playbook -i /opt/hosts /opt/deploy.yml --extra-vars NODE=app --extra-vars username=admin password=cggrL@123 --extra-vars artifact_id=${IMAGE}  --extra-vars version=${VERSION}", 
				execTimeout: 120000, flatten: false, 
				makeEmptyDirs: false, 
				noDefaultExcludes: false, 
				patternSeparator: '[, ]+', 
				remoteDirectory: '', 
				remoteDirectorySDF: false, 
				removePrefix: '', 
				sourceFiles: '*/'
				)
			  ], 
			  usePromotionTimestamp: false, 
			  useWorkspaceInPromotion: false, 
			  verbose: false
			)
		  ]
		)
	  }
	}
  }
}
