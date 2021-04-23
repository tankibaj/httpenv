A very simple image serving environment variables over HTTP. I use this image to test kubernetes ClusterIP service.


## Qick Start

---
---

#### Create deployment and ClusterIp service

- In another window, watch the pods (to see when they are created):

    `kubectl get pods -w`

- Create a deployment for this very lightweight HTTP server:

    `kubectl create deployment httpenv --image=thenaim/httpenv`

- Scale it to 10 replicas:

    `kubectl scale deployment httpenv --replicas=10`


- Expose the HTTP port of our server. We'll create a default ClusterIP service

    `kubectl expose deployment httpenv --port 8888`

- Look up which IP address was allocated:

    `kubectl get service`

---

#### Test ClusterIP service

- Run shpod if not on Linux host so we can access internal ClusterIP

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/tankibaj/public-files/main/kubernetes/shpod.yaml
    kubectl attach --namespace=shpod -ti shpod
    ```

- Let's obtain the IP address that was allocated for our service, programmatically:

    `IP=$(kubectl get svc httpenv -o go-template --template '{{ .spec.clusterIP }}')`

- Send a few requests:

    `curl http://$IP:8888/`

- Too much output? Filter it with jq:
    `curl -s http://$IP:8888/ | jq .HOSTNAME`