library 'jenkins_lib@main'

pipeline {
    agent any
    options {
        ansiColor('xterm')
    }
    environment {
        // AWS_ECR_URL needs to be set in jenkin's config.
        // AWS_ECR_URL could really be any docker registry. we just use ECR so that we don't have to manage it
        REGISTRY="${AWS_ECR_URL}/zkevm-polygon"

        // branch that should get tagged with "latest_$arch" (stable, main, master, etc.)
        LATEST_BRANCH="develop"

        // non-buildkit builds are officially deprecated
        // buildkit is much faster and handles caching much better than the default build process.
        DOCKER_BUILDKIT=1

        GIT_SHORT="${GIT_COMMIT.substring(0,8)}"
    }
    stages {
        stage('build and push') {
            parallel {
                stage('build and push amd64 image') {
                    agent {
                        label 'amd64'
                    }
                    environment {
                        ARCH="amd64"
                    }
                    steps {
                        script {
                            myBuildandPush.buildAndPush()
                        }
                    }
                }
            }

        }
        stage('push latest') {
            parallel {
                stage('maybe push latest_amd64 tag') {
                    agent {
                        label 'amd64'
                    }
                    environment {
                        ARCH="amd64"
                    }
                    steps {
                        script {
                            myPushLatest.maybePushLatest()
                        }
                    }
                }
            }
        }
    }
}
