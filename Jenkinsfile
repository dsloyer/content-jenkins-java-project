pipeline {
  agent any

  stages {
    stage('build') {
      steps {
        sh 'echo env:'
        sh 'env'
        sh 'ant -f build.xml -v'
      }
    }
  }
}








