pipeline {
    agent any

//    options {
//        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
//    }

    stages {
        stage('build') {
            steps {
                sh '/usr/local/ant/bin/ant -f build.xml -v'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
    }
}
