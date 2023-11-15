pipeline {
    agent any

    stages {

        // stage('Get Source') {
        //     steps {
        //         git url: 'https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd.git', branch: 'main'
        //     }
        // }

        // stage('Docker Build Image') {
        //     steps {
        //         script {
        //             dockerapp = docker.build("israeldoamaral/pedelogo-catalogo-jenkins-argocd:${env.BUILD_ID}",
        //               '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
        //         }
        //     }
        // }

        // stage('Docker Push Image') {
        //     steps {
        //         script {
        //                 docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
        //                 dockerapp.push('latest')
        //                 dockerapp.push("${env.BUILD_ID}")
        //             }
        //         }
        //     }
        // }

        stage('Checkout k8s manifest') {
            steps {
                //git branch: 'main', credentialsId: 'github-id', url: 'https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd-manifests.git'
                git credentialsId: '2907cd72-fc88-4efd-bf7c-40630fd8bd04', url: 'git@github.com:israeldoamaral/pedelogo-catalogo-jenkins-argocd-manifests.git', branch: 'main'
            }
        }

        stage('Update and push k8s manifest') {
            environment {
                 tag_version = "${env.BUILD_ID}"
                 GIT_USER_EMAIL = credentials('email_github')
                 GIT_USER_NAME = credentials('nome_usuario_github')
            }
            steps {
                script{
                    sh 'tag=`grep 'pedelogo-catalogo-jenkins-argocd:' k8s/api/deployment.yaml.bkp | awk -F '/' '{print $2}'`'
                    sh 'cat k8s/api/deployment.yaml'
                    sh 'sed -i "s/{{tag}}/pedelogo-catalogo-jenkins-argocd:$tag_version/g" k8s/api/deployment.yaml'
                    // sh 'cat k8s/api/deployment.yaml'
                    // sh 'sed -i "s/{{tag}}/$tag_version/g" k8s/api/deployment.yaml'
                    sh 'cat k8s/api/deployment.yaml'

                    sh 'git config user.email "$GIT_USER_EMAIL"'
                    sh 'git config user.name "$GIT_USER_NAME"'

                    sh 'git add k8s/api/deployment.yaml'
                    sh 'git commit -m "Updated the deploy yaml"'
                    sh 'git push origin main'
                }
            }
        }
    }
}


        //            git push https://github.com/israeldoamaral/pedelogo-catalogo-jenkins-argocd-manifests.git HEAD:main

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