def imageName = 'lucas/movies-loader'
def registry  = 'https://registry.codeops.info'

def docker_repo = 'registry.codeops.info'

node('workers') {
    stage('Checkout') {
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        imageTest.inside{
            sh "python test_main.py"
        }
    }

    stage('Build') {
        docker.build(imageName)
    }

    stage('Push'){
        docker.withRegistry(registry, 'registry') {
            docker.image(imageName).push(commitID())

            if (env.BRANCH_NAME == 'dev') {
                docker.image(imageName).push('dev')
            }
        }
    }

    stage('Analyze'){
        def scannedImage = "${docker_repo}/${imageName}:${commitID()} ${workspace}/Dockerfile"
        writeFile file: 'images', text: scannedImage
        anchore name: 'images',forceAnalyze: true
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}
