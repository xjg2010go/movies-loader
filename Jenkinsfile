def imageName = 'lucas/movies-loader'

node('workers') {
    stage('Checkout') {
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        sh "pwd && rm -f $PWD/reports/*.xml"
        sh "docker run --rm -v $PWD/reports:/app/reports ${imageName}-test"
        sh "ls -l $PWD/reports/*.xml"
        junit "$PWD/reports/*.xml"
    }


    stage('Build') {
        docker.build(imageName)
    }
}
