# Install jenkins

helm install -n cd stable/jenkins -f jenkins/values.yaml --version 1.2.2 --wait

printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo

export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

# Cluster Role binding for jenkins

kubectl create clusterrolebinding jenkins-deploy \
    --clusterrole=cluster-admin --serviceaccount=default:cd-jenkins

# Connect github

ssh-keygen -t rsa -b 4096

