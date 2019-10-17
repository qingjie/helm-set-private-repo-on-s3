```
qzhao-mbp:helm-test qzhao$ kubectl create -f serviceaccount.yaml
serviceaccount/jenkins-helm created
clusterrolebinding.rbac.authorization.k8s.io/jenkins-helm created
qzhao-mbp:helm-test qzhao$ helm search jenkins
NAME          	CHART VERSION	APP VERSION	DESCRIPTION
stable/jenkins	1.7.8        	lts        	Open source continuous integration server. It supports mu...
qzhao-mbp:helm-test qzhao$ helm install --name jenkins --set rbac.create=true,master.runAsUser=1000,master.fsGroup=1000 stable/jenkins
NAME:   jenkins
LAST DEPLOYED: Thu Oct 17 14:22:10 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME           DATA  AGE
jenkins        5     0s
jenkins-tests  1     0s

==> v1/Deployment
NAME     READY  UP-TO-DATE  AVAILABLE  AGE
jenkins  0/1    1           0          0s

==> v1/PersistentVolumeClaim
NAME     STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
jenkins  Bound   pvc-eb3cca38-484e-47a7-a8fd-cb1df57e232f  8Gi       RWO           standard      0s

==> v1/Pod(related)
NAME                    READY  STATUS    RESTARTS  AGE
jenkins-7777488f-xwvkj  0/1    Init:0/1  0         0s

==> v1/Role
NAME                     AGE
jenkins-schedule-agents  0s

==> v1/RoleBinding
NAME                     AGE
jenkins-schedule-agents  0s

==> v1/Secret
NAME     TYPE    DATA  AGE
jenkins  Opaque  2     0s

==> v1/Service
NAME           TYPE          CLUSTER-IP      EXTERNAL-IP  PORT(S)         AGE
jenkins        LoadBalancer  10.105.52.0     <pending>    8080:30113/TCP  0s
jenkins-agent  ClusterIP     10.105.231.160  <none>       50000/TCP       0s

==> v1/ServiceAccount
NAME     SECRETS  AGE
jenkins  1        0s


NOTES:
1. Get your 'admin' user password by running:
  printf $(kubectl get secret --namespace default jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
2. Get the Jenkins URL to visit by running these commands in the same shell:
  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        You can watch the status of by running 'kubectl get svc --namespace default -w jenkins'
  export SERVICE_IP=$(kubectl get svc --namespace default jenkins --template "{{ range (index .status.loadBalancer.ingress 0) }}{{ . }}{{ end }}")
  echo http://$SERVICE_IP:8080/login

3. Login with the password from step 1 and the username: admin


For more information on running Jenkins on Kubernetes, visit:
https://cloud.google.com/solutions/jenkins-on-container-engine
```

login jenkins and update plugin

vi put-bucket-policy.sh
aws iam list-roles | grep kubernetes.newtech.academy
## update some settings like NODE_ROLE_ARN,etc... in put-bucket-policy.sh
./put-bucket-policy.sh

### you can create new job

build helm package and upload it to S3 repo
#### https://github.com/qingjie/kubernetes-course/blob/master/helm/jenkins/Jenkinsfile.build

```
pipeline {
  agent {
    kubernetes {
      label 'helm-pod'
      containerTemplate {
        name 'helm'
        image 'wardviaene/helm-s3'
        ttyEnabled true
        command 'cat'
      }
    }
  }
  stages {
    stage('Run helm') {
      steps {
        container('helm') {
          git url: 'git://github.com/wardviaene/kubernetes-course.git', branch: 'master'
          sh '''
          HELM_BUCKET=helm-rytcufor
          PACKAGE=demo-chart
          export AWS_REGION=eu-west-1
         
          cp -r /home/helm/.helm ~
          helm repo add my-charts s3://${HELM_BUCKET}/charts
          cd helm/${PACKAGE}
          helm dependency update
          helm package .
          helm s3 push --force ${PACKAGE}-*.tgz my-charts
          '''
        }
      }
    }
  }
}
```

helm install from s3 repo
#### https://github.com/qingjie/kubernetes-course/blob/master/helm/jenkins/Jenkinsfile.deploy 
```
pipeline {
  agent {
    kubernetes {
      label 'helm-pod'
      serviceAccount 'jenkins-helm'
      containerTemplate {
        name 'helm-pod'
        image 'wardviaene/helm-s3'
        ttyEnabled true
        command 'cat'
      }
    }
  }
  stages {
    stage('Run helm') {
      steps {
        container('helm-pod') {
          git url: 'git://github.com/wardviaene/kubernetes-course.git', branch: 'master'
          sh '''
          HELM_BUCKET=helm-rytcufor
          PACKAGE=demo-chart
          export AWS_REGION=eu-west-1

          cp -r /home/helm/.helm ~
          helm repo add my-charts s3://${HELM_BUCKET}/charts
          DEPLOYED=$(helm list |grep -E "^${PACKAGE}" |grep DEPLOYED |wc -l)
          if [ $DEPLOYED == 0 ] ; then
            helm install --name ${PACKAGE} my-charts/${PACKAGE}
          else
            helm upgrade ${PACKAGE} my-charts/${PACKAGE}
          fi
          echo "deployed!"
          '''
        }
      }
    }
  }
}
```
