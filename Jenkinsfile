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
        // Construire l'image avec ton nom Docker Hub
        sh 'docker build -t azizghachem/java-web-app .'
    }

    stage('Push Docker Image'){
        // Credentials : Username & Password
        withCredentials([usernamePassword(credentialsId: 'Docker_Hub_Pwd', 
                                         usernameVariable: 'DOCKER_USER', 
                                         passwordVariable: 'DOCKER_PASS')]) {
            sh """
                # Se connecter à Docker Hub
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                # Pousser l'image sur Docker Hub
                docker push azizghachem/java-web-app
            """
        }
    }

    stage('Run Docker Image In Dev Server'){
        def dockerRun = 'docker run -d -p 8080:8080 --name java-web-app azizghachem/java-web-app'

        sshagent(['DOCKER_SERVER']) {
            // Stop et supprime l'ancien conteneur si existant
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@10.0.2.15 docker stop java-web-app || true'
            sh 'ssh ubuntu@10.0.2.15 docker rm java-web-app || true'
            // Lancer le nouveau conteneur
            sh "ssh ubuntu@10.0.2.15 ${dockerRun}"
        }
    }

}
