pipeline {
    agent any

    stages {
        stage('Get Code') {
            steps {
                //Obtener codigo del repositorio
                git 'https://github.com/leyrecanales10/helloword.git'
            }
        }
        
        stage('Build') {
            steps{
                echo 'Eyyy, esto es Python. No hay que compilar nada!!!'
                echo WORKSPACE
                bat 'dir'
            }
        }
        
		stage('Tests'){
			parallel{
				stage('Unit'){
					steps{
						catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
							bat '''
								set PYTHONPATH=%WORKSPACE%
								C:/Users/leyre/AppData/Local/Programs/Python/Python311/Scripts/pytest.exe --junitxml=result-unit.xml test/unit
							'''
						}
					}
				}
        
				stage('Rest') {
					steps {
						catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
							bat '''
								set FLASK_APP=app/api.py
								start C:/Users/leyre/AppData/Local/Programs/Python/Python311/Scripts/flask.exe run
								start java -jar C:/Users/leyre/Dropbox/MasterDevops/CasoPractico1/Wiremock/wiremock-standalone-3.5.4.jar --port 9090  --root-dir test/Wiremock
							'''
							echo "Iniciando sleep"
							sleep time: 30
							echo "¡Sleep completado!"
                   
							retry(3){
							bat '''
								SET PYTHONPATH=%WORKSPACE%
								C:/Users/leyre/AppData/Local/Programs/Python/Python311/Scripts/pytest.exe --junitxml=result-rest.xml test/rest
							'''
							}
                   
						}
                
					}
				}
			}
        }
        stage('Result'){
            steps{
                junit 'result*.xml'
                echo 'FINISH!!'
            }
        }
    }
}
