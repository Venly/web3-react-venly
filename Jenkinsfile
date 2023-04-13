pipeline {
    agent any
    environment {
        GITHUB_CREDS = credentials('GITHUB_CRED')
    }
    options {
        disableConcurrentBuilds()
        timeout(time: 15, unit: 'MINUTES')
    }
    stages {
        stage('Bump version') {
            when {
                anyOf {
                    branch 'develop'
                    branch 'hotfix-*'
                    branch 'release-*'
                }
            }
            steps {
                sh "git config --global user.email \"jenkins@venly.io\""
                sh "git config --global user.name \"Jenkins\""
                sh "npm version prerelease --preid=develop"
            }
        }
        stage('Build') {
            steps {
                sh "npm i"
                sh "npm run build"
            }
        }
        stage('Publish to npmjs') {
            environment {
                NPM_KEY = credentials('NPM_KEY')
            }
            when {
                anyOf {
                    branch 'develop'
                    branch 'hotfix-*'
                    branch 'release-*'
                    branch 'main'
                }
            }
            steps {
                sh "printf '//registry.npmjs.org/:_authToken=' > .npmrc && printf '${NPM_KEY}' >> .npmrc"
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'npm publish'
                    } else {
                        sh 'npm publish --tag ${BRANCH_NAME}'
                    }
                }
                withCredentials([usernamePassword(credentialsId: 'GITHUB_CRED', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ArkaneNetwork/web3-react-venly.git HEAD:refs/heads/${BRANCH_NAME}'
                    sh 'git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ArkaneNetwork/web3-react-venly.git --tags'
                }
            }
            post {
                always {
                    cleanWs(deleteDirs: true, patterns: [[pattern: '.npmrc', type: 'INCLUDE']])
                }
            }
        }
        stage('Merge back to develop') {
            when {
                anyOf {
                    branch 'hotfix-*'
                    branch 'release-*'
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'GITHUB_CRED', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh 'git remote add mergeBack https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ArkaneNetwork/web3-react-venly.git'
                    sh 'git fetch --no-tags mergeBack'
                    sh 'git reset --hard'
                    sh 'git checkout -b develop --track mergeBack/develop'
                    sh 'git merge ${GIT_COMMIT}'
                    sh 'git push mergeBack'
                }
            }
            post {
                always {
                    cleanWs(deleteDirs: true, patterns: [[pattern: '.git', type: 'INCLUDE']])
                }
            }
        }
    }
}
