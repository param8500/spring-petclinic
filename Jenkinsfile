pipeline {
  agent any
  stages {
    stage('Build using Maven') {
      steps {
        sh 'mvn package'
      }
    }

    stage('build docker image and push') {
      steps {
        sh 'docker build -t mypetclinic:$BUILD_ID . '
        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
          sh ''' docker login -u $USER -p $PASS
            docker image tag mypetclinic:$BUILD_ID param8500/mypetclinic:latest
            docker push -a param8500/mypetclinic '''
          }
        
      }
    }
    /*stage('deploy to GKE'){
      steps{
        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', serverUrl: '') {
        sh 'helm upgrade --install petclinc petclinc-chart/ '
        }
      }
    }*/
    stage('Deploy to GKE') {
      steps{
        wrap([$class: 'GCloudBuildWrapper', credentialsId: 'expanded-metric-364406']) {
          gcloud container clusters get-credentials mycluster --region us-central1 --project expanded-metric-364406
          sh 'helm upgrade --install petclinc petclinc-chart/ '
        }
            }
    }    
  }
}