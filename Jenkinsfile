pipeline {

    agent none // MODIFICA: nessun agent globale, scegliamo Mac/Windows per stage

    stages {

        stage('Checkout') {
            agent any // MODIFICA: serve un agent per scaricare la repo (di solito Mac controller)
            steps {
                checkout scm // MODIFICA: ora funziona perché repo è collegata in "Pipeline from SCM"
            }
        }

        stage('Build Parallel') {
            parallel {

                // =========================
                //  BUILD MAC (controller)
                // =========================
                stage('Build macOS') {
                    agent { label 'built-in' } // MODIFICA: eseguito sul Mac (controller Jenkins)

                    steps {
                        script {

                            // MODIFICA: pulizia + build directory separata
                            sh '''
                            rm -rf build_mac
                            mkdir -p build_mac

                            cmake -S . -B build_mac -DCMAKE_BUILD_TYPE=Release
                            cmake --build build_mac
                            '''
                        }
                    }
                }

                // =========================
                // 🪟 BUILD WINDOWS (agent)
                // =========================
                stage('Build Windows') {
                    agent { label 'windows' } // MODIFICA: usa nodo Windows collegato via agent

                    steps {
                        script {

                            // MODIFICA: equivalente Windows di mkdir + build CMake
                            bat '''
                            if exist build_win rmdir /s /q build_win
                            mkdir build_win

                            cmake -S . -B build_win -DCMAKE_BUILD_TYPE=Release
                            cmake --build build_win --config Release
                            '''
                        }
                    }
                }
            }
        }
    }

    post {
        always {

            // MODIFICA: messaggio finale sempre eseguito
            echo "Build completata su Mac e Windows"
        }
    }
}
