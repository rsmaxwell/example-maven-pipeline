pipeline {
  agent {
    kubernetes {
      yamlFile 'KubernetesPod.yaml'
    }
  }

  environment {
    FAMILY = 'linux'
    ARCHITECTURE = 'amd64'
  }


  stages {

    stage('prepare') {
      steps {
        container('tools') {
          dir('project') {
            echo "preparing the application (FAMILY=${env.FAMILY}, ARCH=${env.ARCHITECTURE})"
            checkout([
              $class: 'GitSCM', 
              branches: [[name: '*/main']], 
              extensions: [[
                $class: 'CloneOption',
                noTags: false,   // <-- key bit: fetch tags
                shallow: false,
                depth: 0,
                timeout: 10
              ]], 
              userRemoteConfigs: [[url: 'https://github.com/rsmaxwell/example-java-maven']]
            ])
            sh('./scripts/prepare.sh')
          }
        }
      }
    }

    stage('build/test/package/deploy') {
      steps {
        container('maven') {
          dir('project') {
            echo 'build the application'
            sh('./scripts/build.sh')

            echo 'testing the application'
            sh('./scripts/test.sh')

            echo 'packaging the application'
            sh('./scripts/package.sh')

            echo 'deploying the application'
            sh('./scripts/deploy.sh')
          }
        }
      }
    }
  }
}
