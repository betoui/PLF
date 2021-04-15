# H1 DevOps Challenge

## H2 Dockerfile and Kubernets config

First we install Minikube, we start it and we make sure Docker has the necessary CPU and memory to run with the below requirements. If not, we update accordingly in the settings>resources.

``` bash 
brew install minikube
minikube start --cpus 4 --memory 8192
``` 

Then we build the image with the Dockerfile we have created in the repository. This is a simple application so there is no need to create a directory. I added a tag to make it easier to locate.

``` bash 
docker build -t mock-endpoints .
```

We run our image, using a flag to specify the ports we want it to run on. 

``` bash 
docker run -p 3000:3000 mock-endpoints
```

We configure our local environment to use the Docker daemon inside the Minikube instance 

``` bash 
eval $(minikube docker-env)
```

We apply the files for our deployment. If everything goes according to plan we should create a deployment with its service.

``` bash 
kubectl apply -f deployment.yaml
```

``` bash 
minikube service my-mock-endpoints --url
```

Insert the output of the previous command

``` bash 
curl http://127.0.0.1:62576  
```

In a CI/CD pipeline I would use the Dockerfile in my source-to-image step/task and my deployment.yaml in my deploy step/task. To make my files reusable across multiple deployment environment I would probably use Helm charts.

## H2 Horizontal Scaling

### H3 Manual Horizontal Scaling

1. Update the kubernetes deployment yaml and re-apply it 
2. 
```bash 
kubectl scale deployment.v1.apps/yld-deployment --replicas=10
```
3. on the UI 

In order of preference. 

### H3 Horizontal Auto-scaling

You can also set up a horizontal pod autoscaler based on observed CPU utilization.

## H2 Deployment

As mentioned above I would use Helm charts for my deployments. 

## H2 Update Strategies

You can specify your desired method for an update straegy in the deployment.yaml. The available options are outlined individually below.
### H3 1. Rolling update strategy

Kubernetes system is updated gradually pod-by-pod. There is minimum downtime but it takes slightly longer than a recreation strategy. You cannot have autoscaling with it.
### H3 2. Recreation strategy

You update everything at once with some downtime but it is quicker than a rolling update. 

### H3 3. Canary strategy

You have quick updates for few users with the full rollout coming later.

## H2 Failure Scenarios

Proactively the best thing we can do to prepare for failure scenarios is chaos engineering. The tool I am currently learning to use is LitmusChaos. It is cloud native and very easy to configure on Kubernetes

In the event of an actual failure, I would act according to the severity. As a first step, I would try to get as much available information as possible (in the form of all the available logs or monitoring the events in the cluster) to figure out what is wrong.

