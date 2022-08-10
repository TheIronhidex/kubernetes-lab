pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: maven
            image: maven:alpine
            command:
            - cat
            tty: true
          - name: node
            image: node:16-alpine
            command:
            - cat
            tty: true
        '''
    }
  }
  stages {
    stage('Run maven') {
      steps {
        container('maven') {
          sh 'mvn -version'
        }
        container('node') {
          sh '''
            npm version
            apk add --no-cache \
            python3 \
            py3-pip \
            && pip3 install --upgrade pip \
            && pip3 install --no-cache-dir \
            awscli \
            && rm -rf /var/cache/apk/*
            aws --version
            '''
        }
      }
    }
    stage('AWS') {
        steps {
            container('node') {
            withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-jose', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]){
              sh '''
                aws --version
                aws ec2 describe-instances
                '''
           }    
        }
      }
    }    
  }
}
