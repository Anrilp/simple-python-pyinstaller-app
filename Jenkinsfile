node {
    stage('Build') {
        docker.image('python:2-alpine').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
    }

    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        
        junit 'test-reports/results.xml'
    }

   stage('Deliver') {
    docker.image('cdrx/pyinstaller-linux:python2').inside('--entrypoint=""') {
        sh 'echo $PATH'
        sh 'which pip || echo "pip not found"'
        sh 'which pyinstaller || echo "pyinstaller not found"'
    }
}

}
