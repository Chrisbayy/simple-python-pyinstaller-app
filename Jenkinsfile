pipeline {
    agent any
    environment {
        PATH = "/root/.local/bin:$PATH"
    }
    stages {
        stage('Build') {
            steps {
                sh 'python3 -m pip install --upgrade pip'
                sh 'python3 -m pip install -r requirements.txt'
            }
        }
        stage('Test') {
            steps {
                sh 'pytest tests/test_calc.py'
            }
        }
        stage('Deliver') {
            steps {
                sh 'docker run --rm -v "$PWD":/app -w /app python:3-alpine sh -c "pip install pyinstaller && pyinstaller --onefile sources/calc.py"'
            }
        }
    }
}
