def installPythonDependencies() {
    dir('python-greetings-install') {
        deleteDir()

        echo 'Cloning python-greetings repository...'
        git url: 'https://github.com/mtararujs/python-greetings.git', branch: 'main'

        echo 'Listing files...'
        sh 'ls -la'

        echo 'Creating virtual environment...'
        sh 'python3 -m venv venv'

        echo 'Installing dependencies...'
        sh './venv/bin/python -m pip install -r requirements.txt'
    }
}

def deployToEnv(String envName, String port) {
    dir("deploy-${envName}") {
        deleteDir()

        echo "Deploying to ${envName}..."

        git url: 'https://github.com/mtararujs/python-greetings.git', branch: 'main'

        sh "pm2 delete greetings-app-${envName} || true"

        sh 'python3 -m venv venv'
        sh './venv/bin/python -m pip install -r requirements.txt'

        sh "pm2 start ./venv/bin/python --name greetings-app-${envName} -- app.py --port ${port}"
    }
}

def runTests(String envName) {
    dir("tests-${envName}") {
        deleteDir()

        echo "Running tests on ${envName}..."

        git url: 'https://github.com/mtararujs/course-js-api-framework.git', branch: 'main'

        sh 'npm install'

        sh "npm run greetings greetings_${envName}"
    }
}

pipeline {
    agent any

    stages {

        stage('install-pip-deps') {
            steps {
                echo 'Installing Python dependencies...'
                installPythonDependencies()
            }
        }

        stage('deploy-to-dev') {
            steps {
                deployToEnv('dev', '7001')
            }
        }

        stage('tests-on-dev') {
            steps {
                runTests('dev')
            }
        }

        stage('deploy-to-stg') {
            steps {
                deployToEnv('stg', '7002')
            }
        }

        stage('tests-on-stg') {
            steps {
                runTests('stg')
            }
        }

        stage('deploy-to-preprod') {
            steps {
                deployToEnv('preprod', '7003')
            }
        }

        stage('tests-on-preprod') {
            steps {
                runTests('preprod')
            }
        }

        stage('deploy-to-prod') {
            steps {
                deployToEnv('prod', '7004')
            }
        }

        stage('tests-on-prod') {
            steps {
                runTests('prod')
            }
        }
    }
}
