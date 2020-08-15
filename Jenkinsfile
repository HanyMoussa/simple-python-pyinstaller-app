pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        
        stage('Validate') {
            agent {
                docker {
                    image 'docker-pyyaml'
                }
            }
            steps {
                sh "python3 -c 'import yaml, sys; yaml.safe_load(sys.stdin)' < 1.yaml"
            }
        }
        
        
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
    }
}
