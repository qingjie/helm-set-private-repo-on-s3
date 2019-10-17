
## setup helm repository on S3
#### Please check code in here
#### https://github.com/qingjie/kubernetes-course/blob/master/helm/README.md
#### https://github.com/qingjie/kubernetes-course/blob/master/helm/setup-s3-helm-repo.sh
```
cat README.md
#install helm s3 plugin
helm plugin install https://github.com/hypnoglow/helm-s3.git
#install helm s3 bucket
helm s3 init s3://helm-${RANDOM_STRING}/charts
#add repository to helm
helm repo add my-charts s3://helm-${RANDOM_STRING}/charts
export AWS_REGION=us-east-1
```
```
aws sts get-caller-identity
./setup-s3-helm-repo.sh
helm repo list
helm package demo-chart
helm s3 push ./demo-chart-0.0.1.tgz my-charts
helm search demo-charts
```
