pipeline {
    agent any

    stages {
        stage('build') {
            steps {
                sh '/usr/local/ant/bin/ant -f build.xml -v'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint=true
        }
    }
}
