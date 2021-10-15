def imageName = 'lucas/movies-loader'
def registry  = 'https://registry.codeops.info'

node('workers') {
    stage('Checkout') {
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        sh "docker run --rm -v $PWD/reports:/app/reports ${imageName}-test"
        sh "ls -l $PWD/reports/*.xml"
        junit allowEmptyResults: true, testResults:"$PWD/reports/*.xml"
    }


    stage('Build') {
        docker.build(imageName)
    }

    stage('Push') {
        docker.withRegistry(registry,'registry') {
            docker.image(imageName).push(env.BUILD_ID)
        }
    }
}
