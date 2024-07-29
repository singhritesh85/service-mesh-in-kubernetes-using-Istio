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
Finally you will see two out of two containers are ready as shown in the screenshot below. The second container is for sidecar proxy envoy container. 
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
![image](https://github.com/user-attachments/assets/b1cc9de9-3ba5-4c0d-ab5a-c199d7d51cee)

### Istio supports traffic spliting and hence canary deployment.
To demonstrate canary deployment using Istio I have installed Argo Rollout and deleted earlier deployment, gateway and virtualservice using the command kubectl delete -f kubernetes-original-manifests.yaml and kubectl delete -f gateway-original.yaml.
![image](https://github.com/user-attachments/assets/8ba8ac31-f98b-4ee8-8d1c-87b8cabaa372)
![image](https://github.com/user-attachments/assets/8f049320-8664-4bff-9c0a-376238be9854)
![image](https://github.com/user-attachments/assets/a5b7e030-9e49-4444-aabe-5b41d934ca5c)
![image](https://github.com/user-attachments/assets/b9fc9175-6a92-405a-a28a-6a7fdd4cb7b8)
![image](https://github.com/user-attachments/assets/faefdb4a-9feb-4d16-a4c1-300586b2e1ea)

In **canary deployment** a newer version of Application pods will be created, the older version of application is named as stable version and newer version is named as canary version. A certain percentage (weight) of traffic will be routed to the canary version and rest of the traffic will be routed to the stable version and perform the testing with newer version of Application pods. If every thing is fine with newer version of application then perform rollout and move to newer version of applications.

In the Original manifests file as present in the link https://raw.githubusercontent.com/istio/istio/release-1.22/samples/bookinfo/platform/kube/bookinfo.yaml. I did change in productpage-v1 deployment as shown in the screenshot below and updated manifests file is available with this Repository.
![image](https://github.com/user-attachments/assets/cfe42bbf-bc9d-4467-84fc-112aa6d746ad)
![image](https://github.com/user-attachments/assets/1a940db8-88a1-4bd6-9920-880804ff4018)
![image](https://github.com/user-attachments/assets/7ae10ef0-99ff-4efc-82c3-7006b7e7b92d)
![image](https://github.com/user-attachments/assets/4b085d1c-5f4b-4337-96b9-383fc19c37c3)
![image](https://github.com/user-attachments/assets/e9281714-fc55-4981-b9ab-87cc5e56ad0d)

Now change the image version of application productpage from 1.19.1 to 1.20.1
![image](https://github.com/user-attachments/assets/21a20a8a-0954-489d-a1df-4e033e9a4d7d)
And check if the newer version of application is working properly. If it is working properly then perform rollout.
```
Below commands can be used to perform rollout and check the status after rollout

kubectl argo rollouts promote productpage-v1 -n microservice

kubectl argo rollouts get rollout productpage-v1 -n microservice --watch
```
![image](https://github.com/user-attachments/assets/8f76281f-7777-4d43-a743-5dc94a57bba1)
![image](https://github.com/user-attachments/assets/9d9468a6-81cc-4fcd-98de-62d912d828c8)

![image](https://github.com/user-attachments/assets/ff6d05c4-a6d4-4875-b3e8-8479ca334410)

You can also perform installation of jaeger, kiali, prometheus and grafana with istio as shown in below screenshots.
![image](https://github.com/user-attachments/assets/2e49d7a4-9d60-458c-bf60-1a50396072f4)
![image](https://github.com/user-attachments/assets/1843806d-7937-423a-a75f-0dccfb6a67ea)
