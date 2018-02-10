pipeline {
    agent none

    options {
        buildDiscarder(logRotator(numToKeepStr: '2', artifactNumToKeepStr: '1'))
    }

    stages {
        stage('Unit Tests') {
            agent {
                label 'apache'
            }
            steps {
                sh '/usr/local/ant/bin/ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }
        // Always build on debian (for us, this is the master)
        stage('build') {
            agent {
                label 'apache&&debian'
            }
            steps {
                sh '/usr/local/ant/bin/ant -f build.xml -v'
            }
        }
        stage('deploy debian') {
            agent {
                label 'debian'
            }
            steps {
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
            }
        }
        // Pull from the master (slave is "centos", but not really)
        stage('deploy centos') {
            agent {
                label 'centos'
            }
            steps {
                sh "wget http://192.168.0.202/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "cp rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
            }
        }
        stage("Running on CentOS") {
            agent {
                label 'centos'
            }
            steps {
                sh "wget http://192.168.0.202/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }
        stage("Running on Debian") {
            agent {
                label 'debian'
            }
            steps {
                sh "wget http://192.168.0.202/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
    }
}
