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
