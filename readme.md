## Introduction

This repository is a collection of resources used to experiment with Anypoint service mesh.  
Configuration files and other resources will be stored here.

### Before you start
- Request an Anypoint Service Mesh trial license from MuleSoft. Actioning the trial license may require some time, so it may be wise to send a request a few days before starting on this demo.
- Request the Anypoint permissions that you will need from your organisation. To define the API to be applied through the Service Mesh, the API creator role is required. To access analytics and apply policies, API Manager Environment Administration permission is required.


### Instructions

- Minikube should be started with 4 CPUs if that is being used to run istio.  
  `minikube start --memory=16384 --cpus=4`
- Download and install istio 1.6.8.  
  `curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.6.8 sh -`  
  `istioctl install --set profile=demo`
- Create a namespace for pod deployment.
  `kubectl create namespace mule-apis`
- Enable istio injection so that deployed pods can spin up with sidecar proxies.  
  `kubectl label namespace mule-apis istio-injection=enabled`
- Apply the manifest file for anypoint service mesh (the manifest file used depends on the purpose of installation).  
  `istioctl manifest apply -f manifest-custom.yaml`
- Download and install anypoint service client.  
  `curl -Ls http://anypoint.mulesoft.com/servicemesh/xapi/v1/install > asmctl && chmod +x asmctl`
- Install anypoint service mesh. You will need a anypoint license key and the client id and secret associated with that license key.  
  `asmctl install --clientId=<clientId> --clientSecret=<clientSecret> --license=<license absolute or relative path> --platformUri=<Anypoint Platform Uri>`
- Verify the status of all pods. There should be six pods in total running in the service-mesh namespace.  
  `kubectl get pods -n service-mesh`
- Provision an adapter for the service mesh. The deployment you want to be in the service mesh need to be in the same namespace as the adapter provision.  
  `asmctl adapter create \ --name=<adapter name> \ --namespace=<adapter namespace> \ --size=<adapter plan size> \ --replicas=<amount of replicas for the adapter> \ --clientId=<clientId of the environment or organization> \ --clientSecret=<client secret of the environment or organization> \ --platformUri=<URL of Anypoint Platform>`
- Verify that there is a new adapter pod in Service Mesh pod list.
  `kubectl get pods -n service-mesh`
- Verify the adapter status.  
  `asmctl adapter list`
- Point local docker daemon to minikube's internal docker registry.
  `eval $(minikube -p minikube docker-env)`
- Go to `hello_world_api` folder and build an image.
  `docker build -t hello-world-image .`
- Deploy the application in kubernetes using the yaml file.  
  `kubectl apply -f hello_world_api/deployment.yaml -n mule-apis`
- Change context to `mule-apis` namespace.
  `kubectl config set-context $(kubectl config current-context) --namespace mule-apis`
- Run a pod with curl image in mule-apis namespace and send a request to the service. It should come back with a hello-world response.
  `kubectl run curl-debug  --generator=run-pod/v1  --image curlimages/curl -i -t  --command -- bin/sh`
- This method can be used to send requests to the endpoint for testing purposes later too.
  `kubectl attach curl-debug -c curl-debug -i -t`
- Go back to the project root directory. Fill in environmentId, instanceLabel, assetId, version, apiSpec,apiInstance tags, username and passwordin anypoint-api-auto-create.yaml and apply auto-create YAML file to create your API in Anypoint Platform automatically.  
  `kubectl apply -f anypoint-api-auto-create-template.yaml`
- Verify the endpoint is created on anypoint platform.  
  `asmctl api list`
- Bind your api endpoint.  
  `kubectl apply -f anypoint-bind-hello.yaml`
- Verify the binding status.  
  `asmctl api binding list`
- Verify istio sidecar status.  
  `asmctl management check sidecar --namespace=namespace`
- Log in to Anypoint platform to apply policies and view analytics. You will require API Manager Environment Administrator permission on the environment the API is being created in.
- It may take some time for analytics data to show up on Anypoint Platform. The best way to test the new API is to apply a policy and test if the policy is being honoured.
- Also have a look at the following readings for a better understanding of Anypoint service mesh and service meshes in general.

### Recommended Reading

- [Why use a Service Mesh](https://medium.com/agiledigital/scaling-with-microservices-heres-why-service-mesh-is-a-must-a0c67340205a)
- [Service mesh simplified explanation](https://medium.com/swlh/service-mesh-explained-in-plain-english-8e5505f74ead)
- [All about service mesh](https://github.com/paulbouwer/slide-decks/blob/master/2019/container-camp-au/Decoding-the-Service-Mesh-Landscape.pdf)
- [Mule policy](https://docs.mulesoft.com/api-manager/2.x/policies-mule4)
- [Classloader isolation](https://docs.mulesoft.com/mule-runtime/4.3/about-classloading-isolation)
- [Need a service mesh or not?](https://thenewstack.io/when-you-do-and-dont-need-a-service-mesh/#:~:text=A%20service%20mesh%20provides%20a,an%20orchestration%20platform%2C%20commonly%20Kubernetes.&text=The%20number%20of%20microservices%20you,an%20impact%20on%20your%20needs.)
- [What is zero trust?](https://www.csoonline.com/article/3247848/what-is-zero-trust-a-model-for-more-effective-security.html)
- [Zero trust architecture](https://www.paloaltonetworks.com/cyberpedia/what-is-a-zero-trust-architecture)
- [Service mesh set up](https://kubernetes.io/blog/2020/03/18/kong-ingress-controller-and-istio-service-mesh)
- [Anypoint Analytics](https://docs.mulesoft.com/api-manager/2.x/viewing-api-analytics)
