def imageName = 'movies-loader'
def registry  = '741767866316.dkr.ecr.us-east-1.amazonaws.com'
def region = 'us-east-1'

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
        sh "\$(aws ecr get-login --no-include-email --region ${region}) || true"
        docker.withRegistry("https://${registry}") {
            docker.image(imageName).push(commitID())

            if (env.BRANCH_NAME == 'dev') {
                docker.image(imageName).push('develop')
            }

            if (env.BRANCH_NAME == 'preprod') {
                docker.image(imageName).push('preprod')
            }

            if (env.BRANCH_NAME == 'master') {
                docker.image(imageName).push('latest')
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
