<H2>Automated deployment of Service Mesh (istio) in openshift:</H2>
==========================================================

Installing the Service Mesh involves installing the Elasticsearch, Jaeger, Kiali and Service Mesh Operators, creating and managing a ServiceMeshControlPlane resource to deploy the control plane, and creating a ServiceMeshMemberRoll resource to specify the namespaces associated with the Service Mesh.

<H3>Step 1:</H3>
Service mesh (istio) installation requires below 4 operators to be installed in the OpenShift cluster in the mentioned order, and since all these need to be installed in the openshift-operators namespace and applicable for all namespaces so we will use the default OperatorGroup in the openshift-operators namespace and no need to deploy the OperatorGroup resource, we only need to deploy their subscription:

1. Elasticsearch
2. Jaeger
3. Kiali
4. Service Mesh

<H3>Step 2:</H3>
Once the above operators are installed, create the servicemeshcontrolplane custom resource to manage the resources deployed on service mesh.

<H3>Step 3:</H3>
Once the servicemeshcontrolplane (smcp) is created and ready, deploy the Service Mesh member roll (smmr) custom resource. The smmr describes the namespace(s) that is(are) managed by the servicemesh control place.

Initially the list of members in smmr might be empty and as we know the namespaces that will contribute to the service mesh, we can edit smmr cr to add the namespaces (members) using below oc patch command:

<i>oc patch smmr default -n istio-system --type='json' -p '[{"op": "add", "path": "/spec/members", "value":["'"mynamespace"'"]}]'</i>

Where mynamespace is the namespace that you want to add as member of service mesh. After adding members into the smmr , the control plane is able to control the targeted namespace and to manage its resources as part of the service mesh.

Now you can deploy your application, create VirtualService , Gateway and other service mesh resources.

And to remove a namespace from service mesh use below oc patch command:

<i>oc -n istio-system patch --type='json' smmr default -p '[{"op": "remove", "path": "/spec/members", "value":["'"mynamespace"'"]}]'</i>

