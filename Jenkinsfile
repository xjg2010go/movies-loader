def imageName = 'lucas/movies-loader'
def registry  = 'https://registry.codeops.info'


def commitID() {
    sh 'git rev-parse HEAD' > '.git/commitID'
    def commitID = readFile('git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}


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
            docker.image(imageName).push(commitID())
        }
    }
}
