pipeline {
    agent any

    stages {

        stage('Get Source') {
            steps {
                git url: 'https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd.git', branch: 'main'
            }
        }

        stage('Docker Build Image') {
            steps {
                script {
                    dockerapp = docker.build("israeldoamaral/pedelogo-catalogo-jenkins-argocd:${env.BUILD_ID}",
                      '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }

        stage('Docker Push Image') {
            steps {
                script {
                        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        dockerapp.push('latest')
                        dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Checkout k8s manifest') {
            steps {
                git url: 'https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd-manifests.git', branch: 'main'
            }
        }

        stage('Update and push k8s manifest') {
            environment {
                 tag_version = "${env.BUILD_ID}"
            }
            steps {
                script{
                    withCredentials([gitUsernamePassword(credentialsId: 'github-id', gitToolName: 'Default')]) {
                    sh '''
                    echo $tag_version
                    cat k8s/api/deployment.yaml
                    sed -i "s/{{tag}}/$tag_version/g" k8s/api/deployment.yaml
                    cat k8s/api/deployment.yaml
                    git add k8s/api/deployment.yaml
                    git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                    git remote -v
                    git push https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd-manifests.git HEAD:main
                    '''
                    }
                }
            }
        }



        // stage('Deploy Kubernetes') {
        //     agent {
        //         kubernetes {
        //             cloud 'kubernetes'
        //         }
        //     }
        //     environment {
        //         tag_version = "${env.BUILD_ID}"
        //     }

        //     steps {
        //         sh 'sed -i "s/{{tag}}/$tag_version/g" ./k8s/api.yaml'
        //         sh 'cat ./k8s/api.yaml'
        //         kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kubeconfig')
        //     }
        // }
    }
}