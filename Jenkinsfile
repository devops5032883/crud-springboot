pipeline {
  agent {
    node {
      label 'master'
    }

  }
  stages {
    stage('Build and Unit Test') {
      steps {
        bat '''
            mvn clean install
            '''
      }
    }
    stage('Code Scans') {
      steps {
        script {
          def scannerHome = tool 'SonarQube6.7';
          withSonarQubeEnv('SonarQube6.7') {
            bat '''
mvn package sonar:sonar \
-Dsonar.projectKey=FirstPipeline \
-Dsonar.projectName=FirstPipeline \
-Dsonar.projectVersion=1.0-SNAPSHOT \
-Dsonar.sources=src/main/java \
-Dsonar.language=java \
-Dsonar.java.binaries=target/classes \
-Dsonar.jacoco.reportPaths=target/coverage-reports/jacoco-unit.exec \
'''
          }
        }

      }
    }
    stage('Reporting') {
      steps {
        junit '**/target/surefire-reports/*.xml'
        jacoco(classPattern: '**/target/classes', execPattern: '**/target/coverage-reports/*.exec')
      }
    }
  }
  tools {
    jdk 'JDK1.8'
    maven 'Maven-3.5'
  }
  parameters {
    choice(choices: '''DEV
LV2
LV3
LV4
PRD''', description: 'Which environment?', name: 'level')
    booleanParam(defaultValue: false, description: 'Push to Nexus Snapshots', name: 'nexusSnapshotFlag')
    booleanParam(defaultValue: false, description: 'Push to Nexus Releases', name: 'nexusReleaseFlag')
    booleanParam(defaultValue: false, description: 'Run Fortify Analysis', name: 'runFortifyFlag')
  }
}