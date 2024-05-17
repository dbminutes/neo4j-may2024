### Setting Up a Neo4j Cluster on Kubernetes

#### Prerequisites
- A running Kubernetes cluster.
- `kubectl` command-line tool installed and configured to interact with your cluster.
- Persistent storage provisioner for dynamic volume provisioning.

#### Step-by-Step Guide

1. **Create a Namespace (Optional)**
   It's a good practice to create a separate namespace for your database.

   ```sh
   kubectl create namespace neo4j
   ```

2. **Apply the Service Configuration**
   The `neo4j-service.yaml` file defines a NodePort service to expose Neo4j's HTTP and Bolt ports.

   ```sh
   kubectl apply -f neo4j-service.yaml -n neo4j
   ```

3. **Apply the StatefulSet Configuration**
   The `neo4j-statefulset.yaml` file sets up the StatefulSet for Neo4j.

   ```sh
   kubectl apply -f neo4j-statefulset.yaml -n neo4j
   ```

4. **Verify the Pods and Services**
   Check if the pods are running and the services are properly set up.

   ```sh
   kubectl get pods -n neo4j
   kubectl get svc -n neo4j
   ```

#### Files Explained

**neo4j-statefulset.yaml**

This file defines the StatefulSet for deploying Neo4j with three replicas.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: neo4j
spec:
  serviceName: "neo4j"
  replicas: 3
  selector:
    matchLabels:
      app: neo4j
  template:
    metadata:
      labels:
        app: neo4j
    spec:
      containers:
      - name: neo4j
        image: neo4j:4.4.12-enterprise
        env:
          - name: NEO4J_ACCEPT_LICENSE_AGREEMENT
            value: "yes"
          - name: NEO4J_dbms_mode
            value: "CORE"
          - name: NEO4J_causal__clustering_initial__discovery__members
            value: "neo4j-0.neo4j.default.svc.cluster.local:5000,neo4j-1.neo4j.default.svc.cluster.local:5000,neo4j-2.neo4j.default.svc.cluster.local:5000"
          - name: NEO4J_causal__clustering_discovery__advertised__address
            value: "$(POD_NAME).neo4j.default.svc.cluster.local:5000"
          - name: NEO4J_causal__clustering_transaction__advertised__address
            value: "$(POD_NAME).neo4j.default.svc.cluster.local:6000"
          - name: NEO4J_causal__clustering_raft__advertised__address
            value: "$(POD_NAME).neo4j.default.svc.cluster.local:7000"
          - name: NEO4J_dbms_memory_heap_max__size
            value: "1G"
        ports:
          - containerPort: 7474
          - containerPort: 7687
        volumeMounts:
          - name: data
            mountPath: /data
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 10Gi
```

**neo4j-service.yaml**

This file defines a NodePort service for exposing the Neo4j HTTP and Bolt ports.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: neo4j
spec:
  selector:
    app: neo4j
  ports:
    - protocol: TCP
      port: 7474
      targetPort: 7474
      name: http
    - protocol: TCP
      port: 7687
      targetPort: 7687
      name: bolt
  type: NodePort
```

#### Accessing Neo4j

To access the Neo4j Browser, use the Node IP and NodePort assigned to the service.

1. Find the NodePort:

   ```sh
   kubectl get svc neo4j -n neo4j
   ```

   Look for the `http` port. For example, if the port is `31000`, you can access Neo4j at `http://<NodeIP>:31000`.

2. Use the Bolt protocol for Neo4j drivers at `bolt://<NodeIP>:<BoltPort>`.
