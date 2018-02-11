pipeline {
    agent none

    options {
        buildDiscarder(logRotator(numToKeepStr: '4', artifactNumToKeepStr: '4'))
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
        stage('Build') {
            agent {
                label 'apache&&debian'
            }
            steps {
                sh '/usr/local/ant/bin/ant -f build.xml -v'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                }
            }
        }
        // deploy the jar file on Apache on our Master, which has Apache, and is Debian
        // We want to support wget's from this server
        stage('Deploy to Debian(master)') {
            agent {
                label 'debian'
            }
            steps {
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
            }
        }
        // OK, now the jar file is in the master's web server, in rectangles/all

        stage("Run on Debian") {
            agent {
                label 'debian'
            }
            steps {
                // copy the jar file from the master's website, and run it
                sh "wget http://192.168.0.202/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }

        // For Centos, instantiate a Centos container with JRE, then
        // pull the jar file from the master
        stage("Test on CentOS") {
            agent {
                docker 'nimmis/java-centos:openjdk-8-jre'
            }
            steps {
                sh "wget http://192.168.0.202/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }
    }
}
