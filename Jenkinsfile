pipeline {
    agent none

    environment {
      MAJOR_VERSION = 1
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '4', artifactNumToKeepStr: '4'))
    }

    stages {
        // Invoke junit to run unit tests on 
        stage('Unit Tests') {
            agent {
                label 'apache'
            }
            steps {
                sh '/usr/local/ant/bin/ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }
        // Always build on (for us, this is the Jenkins Master)
        stage('Build') {
            agent {
                label 'apache'
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
        // We want to support wget's from this server, by placing artifacts in the apache website
        stage('Deploy to apache(Jenkins Master)') {
            agent {
                label 'apache'
            }
            steps {
                sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
            }
        }
        // OK, now the jar file is in the Jenkins Master's web server, in rectangles/all

        stage("Run on Debian") {
            agent {
                label 'apache'
            }
            steps {
                // copy the jar file from the Jenkins Master's website, and run it
                sh "wget http://192.168.0.202/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }

        // For Centos, instantiate a Docker container -- Centos with JRE --
        // then pull the jar file from the Jenkins Master, and test it
        stage("Test on CentOS") {
            agent {
                docker 'nimmis/java-centos:openjdk-8-jre'
            }
            steps {
                sh "wget http://192.168.0.202/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }

        // When functional tests (above) succeed, and branch is "dev", copy artifact to green directory in website
        stage('Promote to Green') {
            agent {
                label 'apache'
            }
            when {
                branch 'master'
            }
            steps {
                sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
            }
        }

        // When functional tests (above) succeed, and branch is "dev", copy artifact to green directory in website
        stage('Promote Dev Branch to Master') {
            agent {
                label 'apache'
            }
            when {
                branch 'dev'
            }
            steps {
                echo "Stashing Any Local Changes"
                sh 'git stash'
                echo "Checking Out Dev Branch"
                sh 'git checkout dev'
                echo 'Pulling Branch'
                sh 'git pull origin'
                sh 'git checkout master'
                echo 'Merging Dev into Master Branch'
                sh 'git merge dev'
                echo 'Pushing to Origin Master'
                sh 'git push origin master'
                echo 'Tagging the Release'
                sh "git tag rectangle_${env.BUILD_NUMBER}"
                sh "git push origin rectangle_${env.BUILD_NUMBER}"
            }
            // post {
            //     success {
            //         emailext(
            //             subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Dev Promoted to Master",
            //             body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Dev Promoted to Master":</p>
            //             <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
            //             to: "dsloyer@gmail.com"
            //         )
            //     }
            // }
        }
    }
}
