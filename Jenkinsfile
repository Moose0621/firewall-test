pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
           '''
      }
    }
    stage('....Downloading....') {
      parallel {
        stage('Maven') {
          steps {
            sh '''
                        bash prime-maven-repo-compliance-libs.sh
               '''
          }
        }
        stage('npm') {
          steps {
            echo "...npm runs here"
          }
        }
        stage('pypi') {
          steps {
            echo "...pypi run here"
          }
        }
      }
    }
     stage('Create tar') {
       steps { 
        sh ''' 
                    tar cvzf java-dependencies.tar.gz *.jar && rm *.jar *.pom

           ''' 
        }
     }
     stage('Nexus Lifecycle Evals') {
        parallel {
          stage('Java Scan') {
           steps {
              nexusPolicyEvaluation(iqApplication: 'test-components-la', iqStage: 'operate', iqScanPatterns: [[scanPattern: 'java-dependencies.tar.gz']])
           }
         }
         stage('NPM Scan') {
           steps {
              nexusPolicyEvaluation(iqApplication: 'npm', iqStage: 'operate', iqScanPatterns: [[scanPattern: 'nodeGoat.tar.gz']])
           }
         }
         stage('PyPi Scan') {
           steps {
              nexusPolicyEvaluation(iqApplication: 'PyPi', iqStage: 'operate', iqScanPatterns: [[scanPattern: 'requirements.txt']])
           }
         }
        }
      }
    stage('Firewall Test') {
      steps {
        sh '''
                    bash prime-maven-repo-compliance-libs.sh -v 2
           '''
      } 
    }
    stage('Clean Up') {
       steps { 
         sh ''' 
                    rm *.tar.gz *.jar*
            '''
         }
     }
   }
}

