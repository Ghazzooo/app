pipeline {
    agent any

    stages {

        stage('CI'){
            steps {

                withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')])            {

                sh """
                    docker build . -t ghazooo/python-app:v$BUILD_NUMBER
                    docker login -u ${USERNAME} -p ${PASSWORD}
                    docker push ghazooo/python-app:v$BUILD_NUMBER
                """
                }
              }
        }

        stage('CD'){
            steps {

                withCredentials([file(credentialsId: 'service-account', variable: 'config')]){
                    sh """
                        gcloud auth activate-service-account --key-file=${config}
                        gcloud container clusters get-credentials gke-cluster --zone us-east4-b --project my-project-ghazooo
                        sed -i 's/tag/${BUILD_NUMBER}/g' deployment/deployment.yaml
                        kubectl apply -Rf deployment
                    """
                }
            }
 
        }
    }
}