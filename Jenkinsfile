pipeline {
  agent {
    node {
      label 'test_engineering_jenkins_build_worker'
    }

  }
  stages {
    stage('Initialize') {
      steps {
         sh 'git tag -l'
         sh 'echo ${BRANCH_NAME}'
         sh 'git checkout ${BRANCH_NAME}'
      }
    }

    stage('Style Check') {
      parallel {
        stage('Sonarscanner') {
          options {
            timeout(time: 15, unit: 'MINUTES')
          }
          steps { 
            script {
              withSonarQubeEnv('sonar_server') {
               // PROJECT_VERSION = (script: 'git describe --tags', returnStdout: true)
                // "echo ${PROJECT_VERSION}"
                PROJECT_NAME = (env.JOB_NAME.split("/"))[0]
                sh "C:\\Users\\Jeethu Joseph\\Downloads\\sonar-scanner-cli-4.2.0.1873-windows\\sonar-scanner-4.2.0.1873-windows\\bin\\sonar-scanner -Dsonar.projectName=${PROJECT_NAME}"
              }
              // This sleep is added since the quality gate can get hung up. This is a known bug, and the suggested solution is to add a 10s sleep.
              sleep(10)
              if(env.BRANCH_NAME != 'develop') {
                def qualitygate = waitForQualityGate()
                if (qualitygate.status != "OK") {
                  error "Pipeline aborted due to quality gate coverage failure: ${qualitygate.status}"
                }
              }
            }
          }
        }

      }
    }

    stage('Testing') {
      steps {
        sh 'echo Testing'
      }
    }
	}
}