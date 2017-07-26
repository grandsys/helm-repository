podTemplate(
    label: 'helm-repo', 
    containers: [
        containerTemplate(name: 'jnlp', image: env.JNLP_SLAVE_IMAGE, args: '${computer.jnlpmac} ${computer.name}', alwaysPullImage: true)
    ],
    volumes: [
        persistentVolumeClaim(claimName: env.HELM_REPOSITORY, mountPath: '/var/helm/', readOnly: false)
    ]) {
           
    node('helm-repo') {
        stage('checkout') {
            checkout scm
        }
        stage('check repo'){
            sh '''
            if [ ! -d /var/helm/repo ]
            then
                sudo chmod g+w /var/helm && mkdir -p /var/helm/repo
            fi
            '''
            
            sh '''
            echo "init index.yaml.."
            [ ! -f /var/helm/repo/index.yaml ] && cp ./docs/index.yaml /var/helm/repo/index.yaml || true
            '''
        }
        
        try {
          withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'helm-repo-credential', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME']]) {

            stage('merge') {
                sh '''
                # copy shared charts
                [ -d /var/helm/repo ] && [ "$(ls -A /var/helm/repo)" ] && cp -f /var/helm/repo/* ./docs/
    
                # setup git
                git config --global user.email jenkins@cloud
                git config --global user.name jenkins
                '''
            }
            
            def changes = sh script: "git diff --quiet HEAD", returnStatus: true

            if(changes == 0) {
              echo 'no uncommited changes, no need to package'
            } else {
                stage('git commit & push') {
                    sh """
                    # commit and push
                    git add --all
                    git commit -m '${params.commiter}'
                    git push -u https://${env.USERNAME}:${env.PASSWORD}@github.com/grandsys/helm-repository.git HEAD:master
                    """
                }
            }
          }
        } catch (e) {
            echo "${e}"
            currentBuild.result = FAILURE
        }
        finally {
            
        }
        
    } 
}
