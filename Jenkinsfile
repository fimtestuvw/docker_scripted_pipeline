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

                    dockerImage = docker.build("myimage:${env.BUILD_ID}",
                        "--label \"GIT_COMMIT=${env.GIT_COMMIT}\""
                        + " --build-arg MY_ARG=myArg"
                        + " ."
                    )
                }
            }
        }
        // stage('Push to docker repository') {
        //     when { branch 'master' }
        //     options { timeout(time: 5, unit: 'MINUTES') }
        //     steps {
        //         lock("${JOB_NAME}-Push") {
        //             script {
        //                 docker.withRegistry('https://myrepo:5000', 'docker_registry') {
        //                     dockerImage.push('latest')
        //                 }
        //             }
        //             milestone 30
        //         }
        //     }
    }
}