pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        bat 'gradle archiveJar  archiveDoc'
        bat 'gradle archiveTestsResults'
      }
    }

    stage('mail') {
      steps {
        mail(subject: 'New build', body: 'A new build is on the way', from: 'hm_dadoun@esi.dz', to: 'hm_dadoun@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('sonarqube') {
              bat 'gradle sonarqube'
            }

            waitForQualityGate true
          }
        }

        stage('test reporting') {
          steps {
            cucumber 'reports/*.json'
          }
        }

      }
    }

    stage('deployement') {
      when {
        branch "master"
      }
      steps {
        bat 'gradle publish'
      }
    }

    stage('Slack notification') {
       when {
        branch "master"
      }
      steps {
        slackSend(baseUrl: 'https://hooks.slack.com/services/', token: 'T01T1LSFUV7/B01SP33499Q/IVdIEMaZJiguOqF8ELgV7t2p', attachments: '.', blocks: '.', channel: 'tp-Jenkins', message: 'a new version is deployed', sendAsText: true)
      }
    }

  }
}