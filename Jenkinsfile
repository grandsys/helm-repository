podTemplate(
    label: 'helm-repo', 
    containers: [
        containerTemplate(name: 'jnlp', image: 'henryrao/jnlp-slave', args: '${computer.jnlpmac} ${computer.name}', alwaysPullImage: true)
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
        persistentVolumeClaim(claimName: 'helm-repository', mountPath: '/var/helm/repo', readOnly: false)
    ]) {
           
    node('helm-repo') {
        stage('checkout') {
            checkout scm
        }
        stage('merge') {
            try {
              withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'helm-repo-credential', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME']]) {

                echo "${env.BRANCH_NAME}"

                sh '''
                # copy shared charts
                cp /var/helm/repo/* ./docs/

                # setup git
                git config --global user.email jenkins@cloud
                git config --global user.name jenkins

                '''
                sh """
                # commit and push
                git add --all
                git commit -m 'auto uploading charts'
                git push -u https://${env.USERNAME}:${env.PASSWORD}@github.com/grandsys/helm-repository.git HEAD:master
                """
              }
            } catch (e) {
                echo "${e}"
                currentBuild.result = FAILURE
            }
            finally {
                echo 'well done'
            }
        }
    } 
}
