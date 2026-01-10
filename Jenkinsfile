node {

    stage('SCM Checkout'){
        git url: 'https://github.com/MithunTechnologiesDevOps/java-web-app-docker.git', branch: 'master'
    }

    stage("Maven Clean Package"){
        def mavenHome = tool name: "Maven-3.5.6", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    } 

    stage('Build Docker Image'){
        sh 'docker build -t dockerhandson/java-web-app .'
    }

    stage('Push Docker Image'){
        withCredentials([usernamePassword(credentialsId: 'Docker_Hub_Pwd', 
                                         usernameVariable: 'DOCKER_USER', 
                                         passwordVariable: 'DOCKER_PASS')]) {
            sh """
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                docker push dockerhandson/java-web-app
            """
        }
    }

    stage('Run Docker Image In Dev Server'){
        def dockerRun = 'docker run -d -p 8080:8080 --name java-web-app dockerhandson/java-web-app'

        sshagent(['DOCKER_SERVER']) {
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.20.72 docker stop java-web-app || true'
            sh 'ssh ubuntu@172.31.20.72 docker rm java-web-app || true'
            sh 'ssh ubuntu@172.31.20.72 docker rmi -f $(docker images -q) || true'
            sh "ssh ubuntu@172.31.20.72 ${dockerRun}"
        }
    }

}
