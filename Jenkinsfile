pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "Maven-3.6"
   }

   stages {

      stage('Checkout') {
         steps {
           checkout([$class: 'GitSCM', branches: [[name: '*/develop1']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/sreeni567/game-of-life.git']]])
         }
     }

   stage('Unit test') {
   steps {
           sh label: '', script: 'clean test'
         }
     }

   stage('Sonarqube') {
    environment {
        scannerHome = tool 'sonarqube'
    }
         steps {
     withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
                    }   
    timeout(time: 10, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
               }
            }
     }

  stage('Package creation') {
   steps {
           sh label: '', script: 'clean package'
         }
     }
  stage('Nexus') {
   steps {
           nexusPublisher nexusInstanceId: '1234', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath:    '/var/lib/jenkins/workspace/GOL-Pipeline']], mavenCoordinate: [artifactId: 'gameoflife', groupId: 'com.wakaleo.gameoflife', packaging: 'war', version: '1.0']]]
         }
     }

}
       post {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success {
               junit '**/target/surefire-reports/TEST-*.xml'
                        }
             }
}

