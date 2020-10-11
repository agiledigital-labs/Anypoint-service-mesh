## Introduction
This repository is a collection of resources uses to experiment with Anypoint service mesh.  
Developed services and configuration files will be stored here.

### Instructions
* Minikube should be started with 4 CPUs if that is being used to run istio.  
`minikube start --memory=16384 --cpus=4`  
* Download and install istio 1.6.8.  
`curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.6.8 sh -`  
`istioctl install --set profile=demo`  
* Enable istio injection so that deployed pods can spin up with sidecar proxies.  
`kubectl label namespace default istio-injection=enabled`  
* Apply the manifest file for anypoint service mesh (the manifest file used depends on the purpose of installation).  
`istioctl manifest apply -f manifest-custom.yaml`  
* Build an image for hello_world_api and then deploy the application using the yaml file in kubernetes.  
`kubectl apply -f hello_world_api/deployment.yaml`  
* Install anypoint service client.  
`curl -Ls http://anypoint.mulesoft.com/servicemesh/xapi/v1/install > asmctl && chmod +x asmctl`  
* Rest of the instructions will be available when anypoint service mesh is implemented.  
* Also have a look at the following readings for a better understanding of Anypoint service mesh and service meshes in general.

info dump

https://github.com/paulbouwer/slide-decks/blob/master/2019/container-camp-au/Decoding-the-Service-Mesh-Landscape.pdf

Mule policy:

https://docs.mulesoft.com/api-manager/2.x/policies-mule4

Classloader isolation:

https://docs.mulesoft.com/mule-runtime/4.3/about-classloading-isolation

Service mesh simplified explanation:

https://medium.com/swlh/service-mesh-explained-in-plain-english-8e5505f74ead

Need a service mesh or not?:

https://thenewstack.io/when-you-do-and-dont-need-a-service-mesh/#:~:text=A%20service%20mesh%20provides%20a,an%20orchestration%20platform%2C%20commonly%20Kubernetes.&text=The%20number%20of%20microservices%20you,an%20impact%20on%20your%20needs.

What is zero trust?:

https://www.csoonline.com/article/3247848/what-is-zero-trust-a-model-for-more-effective-security.html

https://www.paloaltonetworks.com/cyberpedia/what-is-a-zero-trust-architecture

Service mesh set up:

https://kubernetes.io/blog/2020/03/18/kong-ingress-controller-and-istio-service-mesh/
