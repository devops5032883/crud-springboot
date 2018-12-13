pipeline {
	agent {
		node {
			label 'master'
		}
	}
	tools {
		jdk 'JDK1.8'
		maven 'Maven-3.5'
	}
	environment {
		groupId = "groupid"
        appName = "appname"
        mavenVersionProperty = "appversion"
    }
	parameters {
		choice(choices: 'DEV\nTEST\nPROD', description: 'Which environment', name: 'level')
		booleanParam(defaultValue: false, description: 'Push to JFrog Snapshots', name: 'jFrogSnapshotFlag')
		booleanParam(defaultValue: false, description: 'Push to JFrog Releases', name: 'jFrogReleaseFlag')
		//booleanParam(defaultValue: false, description: 'Run Fortify Analysis', name: 'runFortifyFlag')
	}
	stages {
		stage('Pull the Code from GIT Repository') {
			steps {
				git 'https://github.com/Kundan2015/crud-springboot.git'
			}
		}
		
		stage('Build and Unit Test') {
			steps {
				bat '''
					mvn clean install -Dmaven.test.failure.ignore=true
				'''
			}
		} /*
		stage('Code Scans') {
			steps {
				script {
					def scannerHome = tool 'SonarQube6.7';
					withSonarQubeEnv('SonarQube6.7') {
						bat '''
							mvn package sonar:sonar \
							-Dsonar.projectKey=springboot-sample \
							-Dsonar.projectName=springboot-sample \
							-Dsonar.projectVersion=1.0-SNAPSHOT \
							-Dsonar.sources=src/main/java \
							-Dsonar.language=java \
							-Dsonar.java.binaries=target/classes \
							-Dmaven.test.failure.ignore=true \
							-Dsonar.jacoco.reportPaths=target/coverage-reports/jacoco-unit.exec \
						'''
					}
				}

			}
		} */
		stage('Reporting') {
			steps {
				junit '**/target/surefire-reports/*.xml'
				jacoco(classPattern: '**/target/classes', execPattern: '**/target/coverage-reports/*.exec')
			}
		}
		
		stage("Upload Artifact to Jefrog Repository") {
			
			environment {
				groupId = readMavenPom().getProperties().getProperty("$groupId")
				appName = readMavenPom().getProperties().getProperty("$appName")
				mavenVersionProperty = readMavenPom().getProperties().getProperty("$mavenVersionProperty")
				
			}
		
			steps {
				bat '''
					IF %level% == DEV (
						mvn clean install deploy:deploy-file -DgroupId=%groupId% -DartifactId=%appName% -Dfile="D:/New Folder/workspace/test_pipeline/target/%appName%-%mavenVersionProperty%.jar" -DrepositoryId=snapshots -Dversion=%mavenVersionProperty% -Dpackaging=jar -Durl=http://10.119.116.89:8040/artifactory/libs-snapshot-local
					)
					IF %level% == PROD (
						mvn clean install deploy:deploy-file -DgroupId=%groupId% -DartifactId=%appName% -Dfile="D:/New Folder/workspace/test_pipeline/target/%appName%-%mavenVersionProperty%.jar" -DrepositoryId=central -Dversion=%mavenVersionProperty% -Dpackaging=jar -Durl==http://10.119.116.89:8040/artifactory/libs-release-local
					)
				'''					
			}
		}
		
		stage("Copy Artifact Plugin") {
		
				steps {
                	copyArtifacts(projectName: 'sourceproject');			
				}
		} 
		
	}
}
