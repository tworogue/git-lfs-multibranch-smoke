pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
        timeout(time: 5, unit: 'MINUTES')
    }
    environment {
        GIT_LFS_SKIP_SMUDGE = '1'
    }
    stages {
        stage('Check Git LFS') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'git lfs version'
                    } else {
                        bat 'git lfs version'
                    }
                }
            }
        }
        stage('Checkout') {
            steps {
                deleteDir()
                script {
                    def prepareGit = '''git init
git config --local filter.lfs.smudge "git-lfs smudge --skip"
git config --local filter.lfs.process "git-lfs filter-process --skip"
'''
                    if (isUnix()) {
                        sh prepareGit
                    } else {
                        bat prepareGit
                    }
                }
                checkout scm
            }
        }
        stage('Verify LFS files') {
            steps {
                script {
                    def expected = [
                        'uuid.txt': '5e7733d8acc94636850cb466aec524e4',
                        'uuid2.txt': 'c49d89a61c3411e9a5555b2af3892239',
                        'uuid,3.txt': '6fd3199a1cb111e9bbaf537fc996ab9b',
                        'uuid 4.txt': '803219bc1cb111e9abbfb7cc7b029948'
                    ]
                    expected.each { fileName, expectedValue ->
                        def actual = readFile(file: fileName, encoding: 'UTF-8').trim()
                        if (actual != expectedValue) {
                            error("LFS content missing or incorrect: ${fileName}. Enable Git LFS pull after checkout in Branch Sources.")
                        }
                    }
                    echo 'All 4 Git LFS files contain the expected data.'
                }
            }
        }
    }
}
