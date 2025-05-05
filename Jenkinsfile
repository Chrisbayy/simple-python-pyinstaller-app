// Jenkinsfile (ubicado en jenkins/Jenkinsfile)
pipeline {
    agent none // No usamos un agente global, sino uno por etapa

    stages {
        // Etapa 1: Comprobar sintaxis (similar al 'Build' original)
        stage('Build / Lint') {
            agent {
                // Usar un contenedor Docker con Python 3
                docker {
                    image 'python:3.10-slim' // Imagen Python 3 ligera
                    reuseNode true // Optimización para reutilizar el workspace del checkout
                }
            }
            steps {
                sh 'python --version' // Muestra la versión de Python usada
                echo 'Compiling Python sources (syntax check)...'
                // py_compile funciona igual en Python 3 para chequeo básico
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }

        // Etapa 2: Ejecutar Pruebas
        stage('Test') {
            agent {
                // Usar el mismo contenedor Docker con Python 3
                docker {
                    image 'python:3.10-slim'
                    reuseNode true
                }
            }
            steps {
                sh 'python --version'
                echo 'Installing testing dependencies...'
                // Instalar pytest dentro del contenedor efímero de esta etapa
                sh 'pip install pytest'
                echo 'Running tests...'
                // Crear directorio para los reportes antes de ejecutar pytest
                sh 'mkdir -p test-reports'
                // Ejecutar pytest (el comando suele ser 'pytest' ahora)
                sh 'pytest --verbose --junit-xml=test-reports/results.xml sources/test_calc.py'
            }
            post {
                // Bloque 'post' se ejecuta después de los 'steps' de la etapa
                always { // Ejecutar siempre, falle o no la etapa
                    echo 'Archiving test results...'
                    // Recopilar y archivar los resultados de las pruebas en formato JUnit
                    junit 'test-reports/results.xml'
                }
            }
        }

        // Etapa 3: Empaquetar con PyInstaller (similar al 'Deliver' original)
        stage('Package with PyInstaller') {
            agent {
                // Usar el mismo contenedor Docker con Python 3
                docker {
                    image 'python:3.10-slim'
                    reuseNode true
                }
            }
            steps {
                sh 'python --version'
                echo 'Installing PyInstaller...'
                // Instalar PyInstaller dentro del contenedor efímero de esta etapa
                sh 'pip install pyinstaller'
                echo 'Running PyInstaller...'
                // Ejecutar PyInstaller para crear un único ejecutable
                // --clean limpia builds anteriores de PyInstaller
                sh 'pyinstaller --onefile --clean sources/add2vals.py'
            }
            post {
                success { // Ejecutar solo si la etapa es exitosa
                    echo 'Archiving executable...'
                    // Archivar el ejecutable generado (estará en la carpeta 'dist')
                    archiveArtifacts artifacts: 'dist/add2vals', followSymlinks: false
                }
            }
        }
    }
}
