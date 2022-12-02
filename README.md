# multidemo
Shows kuma multi-mesh and builtin gateway

This demo will illustrate how simple it is to add another mesh to an existing Kong Mesh setup. It will add the mesh, and also configure a builtin gateway to allow outside traffic onto the mesh. The builtin gateway is a standalone instance of Envoy configured with meshgateway, meshgatewayroute, and meshgatewayinstance objects to act like a K8s Ingress. 

Step 1: Display the Kong Mesh GUI and illustrate that only a default mesh exists. Ideally in Kong internal se-demo-framework clusters this mesh will have mTLS configured, and some existing apps running that are accessible through a Kong API gateway.

Step 2: Apply the “meshtwo.yaml” with kubectl apply -f meshtwo.yaml. Walk through the sections of the file using linux cat/more or vscode to show the mesh creation, as well as the configuration of a meshgateway object, and a meshgatewayroute.  (If using this on a multizone setup, apply this yaml on the global control plane cluster.)

Step 3: Show the GUI now reflects the new mesh exists using the pulldown. Default allow-any traffic permission and traffic route objects are created automatically. The new meshgateway and meshgatewayroute objects are also available to examine.

Step4: apply the “counter.yaml” with kubectl apply -f counter.yaml. Walk through the sections of this file, it creates the mesh, and then creates the demo-app deployment and service, as well as the redis deployment and service to set up the kuma counter demo. In addition it sets up the meshgateway instance envoy pod to be the running gateway binary associated with the meshgateway and meshgateway route configuration objects. All of the running containers will be present in the kuma-demo namespace. (If using this on a multizone setup, apply this yaml on one of the zone clusters.)

Step 5: “kubectl get all -n kuma-demo will list all of the created objects for the app and the new gateway. Run this command and pick up the external IP address (load balancer IP) associated to the gateway by the K8s environment.

Step 6: Go to a browser on your local system and access the assigned load balancer IP. Show that you are accessing the counter app through the IP address associated to the gateway, te version is 2.0, and the listed “Zone” is “multidemo”.  This is not actually a Zone in the mesh, that name was just fed to the redis image at runtime so it would be clear to differentiate this redis backend from anything else running on the mesh. In a real-world implementation you can expose multiple services through a builtin gateway using specific host/path combinations similar to a K9s Ingress object.

So we have shown how easy it is to set up a completely separate mesh instance that is managed by the same common control plane as the default mesh, and visible through the same GUI. This setup ensures the new mesh is on a separate mTLS certificate authority, so that pods from the new mesh cannot accidentally access services running in the default mesh, and vice-versa. 
