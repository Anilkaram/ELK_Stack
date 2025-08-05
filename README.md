# ELK_Stack
The ELK Stack is a powerful open-source platform for searching, analyzing, and visualizing data in real time using Elasticsearch, Logstash, and Kibana.

# Kibana dashboard overview for logging
<img width="1920" height="1080" alt="Screenshot (304)" src="https://github.com/user-attachments/assets/f3104fd9-62b4-4e07-ab70-dd2162469431" />
<img width="1920" height="1080" alt="Screenshot (305)" src="https://github.com/user-attachments/assets/052769da-4a41-4701-9722-fe70bf1164c5" />

# Application 
<img width="1920" height="1080" alt="Screenshot (303)" src="https://github.com/user-attachments/assets/0e41874a-b1da-4743-b69e-4527255a841a" />

# installation 

1. Prerequisites
- A running K8S cluster and kubectl access.
- Node/cluster resources (at least 4 CPUs, 8GB RAM recommended).
- Helm installed locally to manage deployments.

2. Create a Namespace for Logging
- kubectl create namespace kube-logging

3. Add the Elastic Helm Repository
- helm repo add elastic https://helm.elastic.co
- helm repo update

4. Deploy Elasticsearch
- helm install elasticsearch elastic/elasticsearch -n kube-logging --set replicas=1
- kubectl get pods -n kube-logging

5. Deploy Kibana
- helm install kibana elastic/kibana -n kube-logging
- kubectl get pods -n kube-logging
- Expose Kibana for access (use kubectl port-forward or a LoadBalancer/Ingress)
  
6. Configure and Deploy Filebeat
- filebeat-values.yaml
  filebeatConfig:
  filebeat.yml: |-
    filebeat.inputs:
      - type: container
        paths:
          - /var/log/containers/*.log
    processors:
      - add_kubernetes_metadata:
          host: ${NODE_NAME}
          matchers:
            - logs_path:
                logs_path: "/var/log/containers/"
    output.elasticsearch:
      hosts: ["http://elasticsearch-master.kube-logging.svc.cluster.local:9200"]
      username: "elastic"
      password: "<ELASTIC_PASSWORD>"
(Replace with your Elasticsearch endpoint and credentials as needed.)

7. Deploy Filebeat as a DaemonSet with Helm:
- helm install filebeat elastic/filebeat -n kube-logging -f filebeat-values.yaml

8. Check and Visualize Logs in Kibana
- Navigate to http://localhost:5601
- Go to Discover.
- Create a new data view: filebeat-*.
- You should see container logs with Kubernetes metadata.

You now have a robust, production-ready Kubernetes logging setup using Filebeat and the ELK Stack in your K8S cluster. All container logs will be visible and searchable in Kibana, with powerful metadata for filtering!
