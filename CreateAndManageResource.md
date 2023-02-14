# Create and Manage Cloud Resources - Challenge Lab
You can find the Create and Manage Cloud Resources Qwiklab [here](https://www.cloudskillsboost.google/quests/120)

## Task 1
Create a project jumphost instance

```
gcloud compute instances create nucleus-jumphost-131 \
    --network nucleus-vpc \
    --zone us-east1-b  \
    --machine-type f1-micro  \
    --image-family debian-11  \
    --image-project debian-cloud \
    --scopes cloud-platform \
    --no-address
```

## Task 2
Create a Kubernetes service cluster
```
gcloud config set compute/zone us-west4-b

gcloud container clusters create nucleus-webserver1

gcloud container clusters get-credentials nucleus-webserver1

kubectl create deployment hello-app --image=gcr.io/google-samples/hello-app:2.0

kubectl expose deployment hello-app --type=LoadBalancer --port 8080

kubectl get service
```

## Task 3
Set up an HTTP load balancer

Given config file
```
cat << EOF > startup.sh
    #! /bin/bash
    apt-get update
    apt-get install -y nginx
    service nginx start
    sed -i -- 's/nginx/Google Cloud Platform -'"\$HOSTNAME"'/' 
    /var/www/html/index.nginx-debian.html
    EOF
```

Create an instance template
```
gcloud compute instance-templates create nginx-template \
--metadata-from-file startup-script=startup.sh
```

Create a target pool
```
gcloud compute target-pools create nginx-pool
```

Create a managed instance group 
```
gcloud compute instance-groups managed create nginx-group \
--base-instance-name nginx \
--size 2 \
--template nginx-template \
--target-pool nginx-pool

gcloud compute instances list
```

Create a firewall rule to allow traffic (80/tcp)
```
gcloud compute firewall-rules create grant-tcp-rule-934 --allow tcp:80

gcloud compute forwarding-rules create nginx-lb \
--region us-west4 \
--ports=80 \
--target-pool nginx-pool

gcloud compute forwarding-rules list
```

Create a health check
```
gcloud compute http-health-checks create http-basic-check

gcloud compute instance-groups managed set-named-ports nginx-group --named-ports http:80
```

Create a backend service and attach the manged instance group
```
gcloud compute backend-services create nginx-backend --protocol HTTP --http-health-checks http-basic-check --global

gcloud compute backend-services add-backend nginx-backend --instance-group nginx-group --instance-group-zone us-west4-b --global
```

Create a URL map and target HTTP proxy to route requests to your URL map
```
gcloud compute url-maps create web-map --default-service nginx-backend

gcloud compute target-http-proxies create http-lb-proxy --url-map web-map
```

Create a forwarding rule
```
gcloud compute forwarding-rules create http-content-rule --global --target-http-proxy http-lb-proxy --ports 80

gcloud compute forwarding-rules list
```