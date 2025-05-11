node {
    def imageName = "juberalam308/test"
    def tag = "${env.BUILD_NUMBER}"

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') {
        sh "docker build -t ${imageName}:${tag} ."
    }

    stage('Test image') {
        sh "docker run --rm ${imageName}:${tag} echo 'Tests passed'"
    }

    stage('Push image') {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push ${imageName}:${BUILD_NUMBER}
                docker logout
            '''
        }
    }

    stage('Trigger ManifestUpdate') {
        echo "triggering updatemanifest job"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}
