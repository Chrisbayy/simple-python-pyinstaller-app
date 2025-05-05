pipeline {
    agent {
        docker {
            image 'python:3'   // Aquí usamos una imagen oficial de Python.
            label 'python'     // Puedes ajustar el label si tienes varios nodos.
            args '-u root:root' // Usar el usuario root en Docker para evitar problemas de permisos
        }
    }
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            steps {
                // Asegúrate de tener el código en el contenedor para que sea accesible
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}
