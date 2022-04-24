pipeline {
  agent any

  stages {
    stage('Logging and Verifying') {
      parallel {
        stage('Logging Version') {
          steps {
            bat 'java -version'
            bat 'mvn --version'
            bat 'git --version'
          }
        }

        stage('Verifying POM') {
          steps {
            fileExists 'pom.xml'
          }
        }

        stage('Verifying Maven Settings') {
          steps {
            fileExists 'settings.xml'
          }
        }

      }
    }

    stage('Build') {
      steps {
        bat 'mvn clean package -DskipTests=true -s settings.xml -P source,javadoc,license'
      }
    }

    stage('Test') {
      steps {
        bat 'mvn test -s settings.xml'
      }
    }

    stage('Clean Code') {
      parallel {
        stage('Check Style') {
          steps {
            bat 'mvn checkstyle:check -s settings.xml -P checkstyle'
          }
        }

        stage('Sonar') {
          steps {
            bat 'mvn sonar:sonar -s settings.xml -P sonar'
          }
        }

      }
    }

    stage('Making Site') {
      steps {
        bat 'mvn site:site site:stage -s settings.xml -P site,javadoc,changelog,test-report'
      }
    }

    stage('Publishing Site') {
      steps {
        bat 'mvn scm-publish:publish-scm -s settings.xml -P publish'
      }
    }

    stage('Deploying to Artifactory') {
      steps {
        bat 'mvn deploy -s settings.xml -p artifactory'
      }
    }

  }
}
