pipeline {
    agent {
        docker {
            image 'python:3'
        }
    }
    environment {
        PATH = "/root/.local/bin:$PATH"
    }
    stages {
        stage('Build') {
            steps {
                sh 'python -m pip install --upgrade pip'
                sh 'pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                sh 'pytest tests/test_calc.py'
            }
        }
        stage('Deliver') {
            steps {
                sh 'pip install pyinstaller'
                sh 'pyinstaller --onefile sources/calc.py'
            }
        }
    }
}
