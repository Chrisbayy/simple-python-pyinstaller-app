pipeline {
  agent { docker { image 'python:3.7.2' } }
  options {
    skipStagesAfterUnstable()
  }
  stages {
    stage('Build') {
      steps {
        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        stash(name: 'compiled-results', includes: 'sources/*.py*')
      }
    }
    stage('Test') {
      steps {
        sh '''
          pip install pytest
          mkdir -p test-reports
          pytest --junit-xml=test-reports/results.xml sources/test_calc.py
        '''
      }
      post {
        always {
          junit 'test-reports/results.xml'
        }
      }
    }
    stage('Deliver') {
      steps {
        sh 'pip install pyinstaller'
        sh 'pyinstaller --onefile sources/add2vals.py'
      }
      post {
        success {
          archiveArtifacts 'dist/add2vals'
        }
      }
    }
  }
}
