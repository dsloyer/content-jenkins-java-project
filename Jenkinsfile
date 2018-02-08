pipeline {
  agent any

  stages {
    stage('build') {
      steps {
        bash 'echo env:'
        bash 'env'
        bash 'ant -f build.xml -v'
      }
    }
  }
}









