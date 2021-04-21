def dockerImage
//jenkins needs entrypoint of the image to be empty
def runArgs = '--entrypoint \'\''
pipeline {
    agent {
        label 'master'
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '100', artifactNumToKeepStr: '20'))
        timestamps()
    }
    stages {
        stage('Build') {
          //  options { timeout(time: 30, unit: 'MINUTES') }
            steps {
                script {
                    def commit = checkout scm
                    println(commit)
                    // we set BRANCH_NAME to make when { branch } syntax work without multibranch job
                    env.BRANCH_NAME = commit.GIT_BRANCH.replace('origin/', '')
                    //commit = {GIT_BRANCH=origin/main,
                    // GIT_COMMIT=e40004c967ac6f8a624e9b2b389fe55b6d43ef5a,
                    // GIT_PREVIOUS_COMMIT=6dc52a75f77864eb5c25c855d5bdb41c8f6eb1be,
                    //  GIT_URL=https://github.com/fimtestuvw/docker_scripted_pipeline}

                    // echo 'BRANCH_NAME=${env.BRANCH_NAME}' //this one just print out the whole string :
                    // echo 'BRANCH_NAME=${env.BUILD_ID}'
                    print("env.BRANCH_NAME=${env.BRANCH_NAME}")
                    println("env.BUILD_ID=${env.BUILD_ID}")

                    dockerImage = docker.build("my-docker-local/myimage:${env.BUILD_ID}",
                        "--label \"GIT_COMMIT=${env.GIT_COMMIT}\""
                        + " --build-arg MY_ARG=myArg"
                        + " ."
                    )
                }
            }
        }
        stage('Push to docker repository') {
            when {
                expression {env.BRANCH_NAME == 'main'}

            }
            // options { timeout(time: 5, unit: 'MINUTES') }
            steps {
                lock("${JOB_NAME}-Push") {
                    script {
                        docker.withRegistry('https://auditsg.jfrog.io') {
                            sh 'docker login -u fimtestxyz@gmail.com -p M0t0r0la@@ https://auditsg.jfrog.io'
                            println('------')
                            sh 'printenv | sort'
                            println("${env.BUILD_ID}")
                            println("${BUILD_ID}")
                            println("${JOB_NAME}-Push")
                            println('---------')
                            dockerImage.push("${BUILD-ID}")
                        }
                    }
                }
            }
        }
    }
}