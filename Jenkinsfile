pipeline {
  environment{
        BUILD_N = "${currentBuild.getNumber()}"
    }

    agent any
    stages {
        stage('Checkout') {
            steps {
                 checkout scmGit(branches: [[name: '*/master']], extensions: [],
userRemoteConfigs: [[url: 'https://github.com/sachintalwar/todoapi.git']])
                echo 'successful checkout'
} }
  stage('Build jar ') {
            steps {
                script {
                     sh 'mvn clean install -DskipTests'
                    echo 'Code Compilation successful'
                }
} }
        stage('Build Image using Docker file ') {
            steps {
                script {
                     def imageTag = "talwarsachin4/todoapi:1.${BUILD_N}"
                    docker.build(imageTag, '.')
                    echo 'successful Build Docker Image'
                }
} }
        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'c4e5d2b2-485b-4095-8217-e90e17c06dce', url: 'https://index.docker.io/v1/') {
                        def imageTag = "talwarsachin4/todoapi:1.${BUILD_N}"
                        docker.image(imageTag).push()
                        echo 'successful Push to Docker Hub'
                    }
} }
}
        stage('Kubernetes Deployment'){
			 steps {
                script {
                    sh 'sed -ie "s\\todoapi:1.BUILDNUMBER\\todoapi:1.${BUILD_N}\\g" kubernetes-config.yaml'
                   kubeconfig(credentialsId: '6dc9c4df-6a14-4228-841d-10a562110c31', serverUrl: 'https://kubernetes.docker.internal:6443') {
                    def kubeConfig = readFile 'kubernetes-config.yaml'
                        sh "kubectl apply -f kubernetes-config.yaml"
                        echo 'successful Deployment to Kubernetes Cluster'
                    }
                    }
} }

}
}