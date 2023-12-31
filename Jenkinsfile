def label = "build"
  def env = "perf"
  podTemplate(label: label, yaml: """
  apiVersion: v1
  kind: Pod
  metadata:
    labels:
      name: build
  spec:
    serviceAccount: jenkins-admin
    containers:
    - name: build
      image: prakrit55/jenkinsagent
      command:
      - cat
      tty: true
  """
  ) {
      node (label) {

          stage ('Checkout SCM'){
            git credentialsId: 'git', url: 'https://github.com/prakrit55/admin-deployment', branch:  "${env}"
          }

          stage ('Helm Chart') {
            container('build') {
                withCredentials([usernamePassword(credentialsId: 'jfrog', usernameVariable: 'username', passwordVariable: 'password')]) {
                      sh '/usr/local/bin/helm repo add etccompany-helm-local  https://etccompany.jfrog.io/artifactory/etccompany-helm-local --username $username --password $password'
                      sh "/usr/local/bin/helm repo update"
                      sh "/usr/local/bin/helm upgrade  --install --force micro-services-admin  --namespace ${env} -f values.yaml etccompany-helm-local/micro-services-admin"
                      sh "/usr/local/bin/helm list -a --namespace ${env}"
                      sh "rm -rf values.yaml"
              }
          }
          }
      }
  }