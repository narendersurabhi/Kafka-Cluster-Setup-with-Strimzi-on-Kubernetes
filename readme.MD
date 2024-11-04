# Kafka Cluster Setup with Strimzi on Kubernetes

This guide walks through the steps to set up a Kafka cluster using Strimzi on a Kubernetes cluster.

## Prerequisites
- Kubernetes cluster up and running
- `kubectl` command-line tool installed
- Helm installed

## Installation Steps

### Step 3: Install Strimzi Operator
Add the Strimzi Helm repository and install the Strimzi Kafka Operator:
```sh
helm repo add strimzi https://strimzi.io/charts/
helm repo update
helm install strimzi-kafka-operator strimzi/strimzi-kafka-operator --namespace kafka
```

### Step 4: Install Strimzi CRDs
Apply the Strimzi CRDs:
```sh
kubectl apply -f https://strimzi.io/install/latest?namespace=kafka
```

### Step 5: Create Kafka Cluster
Create a YAML configuration file named my_kafka_cluster.yaml with the following content:
```sh
apiVersion: kafka.strimzi.io/v1beta2
kind: Kafka
metadata:
  name: my-cluster
  namespace: kafka
spec:
  kafka:
    version: 2.8.0
    replicas: 3
    listeners:
      - name: plain
        port: 9092
        type: internal
        tls: false
    storage:
      type: persistent-claim
      size: 10Gi
      class: standard
      deleteClaim: false
  zookeeper:
    replicas: 3
    storage:
      type: persistent-claim
      size: 5Gi
      class: standard
      deleteClaim: false
  entityOperator:
    topicOperator: {}
    userOperator: {}
```

### Step 6: Apply Kafka Cluster Configuration
Apply the Kafka cluster configuration:
```sh
kubectl apply -f my_kafka_cluster.yaml
```

### Verification
Check the status of your Kafka cluster:
```sh
kubectl get kafka -n kafka
```

### Access Kafka Brokers
Access the Kafka brokers using the external listener:
```sh
kubectl port-forward -n kafka svc/my-cluster 9092:9092
```


## To delete the setup
Delete topic
```sh
kubectl delete -f kafka-topic.yaml
```

To delete cluster
```sh
kubectl delete -f kafka-cluster.yaml
```
