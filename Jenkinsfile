pipeline {
  options {
    disableConcurrentBuilds()
  }
  agent {
    dockerfile {
      args '-u root:root'
    }
  }
  environment {
    CF_HOME='/root' // Revert override from Jenkins global env
    CF_API="${env.CF_API}"
    CF_LOGIN=credentials('contentful-proxy.cloudfoundry.login')
    CF_ORG="${env.CF_ORG}"
    CF_SPACE="${env.BRANCH_NAME == 'master' ? 'test' : 'production'}"
  }
  stages {
    stage('Login to CF') {
      steps {
        sh 'cf login -a ${CF_API} -u ${CF_LOGIN_USR} -p "${CF_LOGIN_PSW}" -o ${CF_ORG} -s ${CF_SPACE}'
      }
    }
    stage('Deploy to CF') {
      environment {
        CF_APP_NAME="contentful-proxy${env.CF_SPACE == 'production' ? '' : '-' + env.CF_SPACE}"
      }
      steps {
        configFileProvider([configFile(fileId: "contentful-proxy.${env.CF_SPACE}.vars.yml", targetLocation: 'vars.yml')]) {
          sh 'cf push ${CF_APP_NAME} -f manifest.yml --vars-file vars.yml'
        }
      }
    }
  }
}
