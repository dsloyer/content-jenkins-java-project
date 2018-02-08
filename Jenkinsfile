pipeline {
  agent any

  stages {
    stage('build') {
      steps {
        sh 'echo env: $env'
        sh 'ant -f build.xml -v'
      }
    }
  }
}







