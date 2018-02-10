pipeline {
    agent {
        label 'master'
    }

//    options {
//        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
//    }

    stages {
        stage('Unit Tests') {
            steps {
                sh '/usr/local/ant/bin/ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }
        stage('build') {
            steps {
                sh '/usr/local/ant/bin/ant -f build.xml -v'
            }
        }
        stage('deploy') {
            steps {
                sh 'echo env.BUILD_NUMBER: ${env.BUILD_NUMBER}'
                // sh 'cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
    }
}
