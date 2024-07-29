# service mesh in kubernetes using Istio
![image](https://github.com/user-attachments/assets/a97c146b-2efc-4ffa-898e-dcd0c8492f34)
When considering microservice without service mesh, every service can communicate with every other service and this commmunication happens in plain text. 

<br><br/>
![image](https://github.com/user-attachments/assets/b72e80cd-5322-4700-a8f6-51e771cb7723)
Istiod imparts service discovery, configuration and certificate management. Communication configuration (which microservices are allowed to communicate and which services are not allowed to communicate), metrics, security and tracing will be taken care by sidecar proxy container. After implementation of service mesh using istio no services will be allowed to communicate directly it will be communicated through the **sidecar proxy envoy container** as shown in the diagram above.

<br><br/>
**Architecture diagram for Service mesh using istio is as shown below.**
![image](https://github.com/user-attachments/assets/e03bde4b-edbe-428a-ae64-530e5f852163)

```
Explanation of different components for istio in brief.
1. Pilot, Pilot is responsible for traffic management and routing.
2. Citadel, Citadel is responsible for secure communication between the microservices.
3. Galley, Galley is responsible for configuration management.
4. Mixer, Mixer collects telemetry data.
```

For the demonstration purpose I have used the microservice as provided on the Isio site https://istio.io/latest/docs/setup/getting-started/ in the URL https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/platform/kube/bookinfo.yaml. Details regarding the microservice is available on https://istio.io/latest/docs/examples/bookinfo/.

I had deployed the microservice on the eks-cluster and eks cluster was created using the terraform script as available with the Repository https://github.com/singhritesh85/terraform-eks-withaddons.git. Istio is not yet installed on the eks cluster. 

As shown in the screenshot below at present only one out of one container is ready. It means sidecar proxy envoy container is not yet created in the pod. As I mentioned I had not installed Istio till now.
![image](https://github.com/user-attachments/assets/1eff6214-a2f6-4356-b0cd-de29c1045267)

Now delete the entire namespace and create a fresh one (namespace) then install isto and deploy the microservice as shown in the screenshot below.
![image](https://github.com/user-attachments/assets/51dc3242-06e2-409f-ab44-8be835749231)

```
You must label the namespace with istio-injection=enabled as written below
kubectl label namespace microservice istio-injection=enabled

Now check the labels using the command as shown below
kubectl get ns --show-labels
```
Finally you will see two out of two containers are ready as shown in the screenshot below. The second container is for sedecar proxy envoy container. 
![image](https://github.com/user-attachments/assets/ea7ac4cf-2530-4ce5-a17a-2de1047dc540)

You can observe that in namespace istio-system there is a service named as istio-ingressgateway as shown in the screenshot below. Istio ingress gateway provides the similar functionality as that of ingress-controller. In ingress controller you need to create ingress rule however here you need to create gateway as well as virtual service.
![image](https://github.com/user-attachments/assets/3014a83e-a450-485c-aa65-e3d8ce0a9e4b)

I had provided the SSL certificate through the kubernetes secret as shown below.
![image](https://github.com/user-attachments/assets/2559d9cd-c669-4f25-a4d5-27971a272a79)

```
kubectl create secret tls ecommerce-tls-secret -n istio-system --key mykey.key --cert STAR_singhritesh85_com.crt
```

Screenshot for yaml file to create gateway and virtual service is as shown below.
![image](https://github.com/user-attachments/assets/3030c254-6e66-4b9f-bff4-9f146742e9b3)
![image](https://github.com/user-attachments/assets/61b5f691-3e94-4e57-9fa1-3b9c442a8cae)
![image](https://github.com/user-attachments/assets/18798d7d-ed47-4cd9-8361-60dcc8e27c1e)
![image](https://github.com/user-attachments/assets/fcf9c729-89b9-423f-aabf-bf94e80f3716)

Now do the entry in Rout53 to create record set for DNS Name of isto-ingressgateway service and create the URL and access your application through that URL.
![image](https://github.com/user-attachments/assets/65f8ed5c-ae04-4f4a-a49c-857a5a178567)
![image](https://github.com/user-attachments/assets/47c09078-e667-4e11-9d34-b7211c72eff8)
