pipeline {
    agent {
        docker { image 'python:2-alpine' }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                stash(name: 'compiled-results', includes: 'sources/*.py*') 
            }
        }
    }
}
stage('Test') {
    agent {
        docker {
            image 'qnib/pytest'
        }
    }
    steps {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
    }
    post {
        always {
            junit 'test-reports/results.xml'
        }
    }
}
pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') { //1
            agent {
                docker {
                    image 'qnib/pytest' //2
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py' //3
            }
            post {
                always {
                    junit 'test-reports/results.xml' //4
                }
            }
        }
    }
}
stage('Deliver') {
    agent {
        docker {
            image 'cdrx/pyinstaller-linux:python2'
        }
    }
    steps {
        sh '/root/.pyenv/shims/pyinstaller --onefile sources/add2vals.py'
    }
    post {
        success {
            archiveArtifacts 'dist/add2vals'
        }
    }
}
pipeline {
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'python:2-alpine'
                }
            }
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            }
        }
        stage('Test') {
            agent {
                docker {
                    image 'qnib/pytest'
                }
            }
            steps {
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') { //1
            agent {
                docker {
                    image 'cdrx/pyinstaller-linux:python2' //2
                }
            }
            steps {
                sh '/root/.pyenv/shims/pyinstaller --onefile sources/add2vals.py' //3
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals' //4
                }
            }
        }
    }
}