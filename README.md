A very simple image serving environment variables over HTTP. I use this image to test kubernetes ClusterIP service.

<br/>


## Quick Start

### Docker

- Run docker container and expose port

  ```bash
  docker run --rm -p8888:8888 thenaim/httpenv
  ```

  

- Test endpoint

  ```bash
  curl http://localhost:8888
  
  curl http://localhost:8888/blue
  
  curl http://localhost:8888/green
  
  curl http://localhost:8888/red
  ```

<br/>

### Kubernetes

#### Create deployment and service

- In another window, watch the pods (to see when they are created):

    ```bash
    kubectl get pods -w
    ```

    

- Create a deployment for this very lightweight HTTP server:

    ```bash
    kubectl create deployment httpenv --image=thenaim/httpenv
    ```

    

- Scale it to 10 replicas:

    ```bash
    kubectl scale deployment httpenv --replicas=10
    ```
    
    


- Expose the HTTP port of our server. We'll create a default ClusterIP service

    ```bash
    kubectl expose deployment httpenv --port 8888
    ```

    

- Look up which IP address was allocated:

    ```bash
    kubectl get service
    ```
    
    

#### Test endpoint

- Let's obtain the IP address that was allocated for our service:

    ```bash
    IP=$(kubectl get svc httpenv -o go-template --template '{{ .spec.clusterIP }}')
    ```
    



- Generate URL

  ```bash
  echo http://$IP:8888/
  ```



- Run Busybox to access ClusterIP

    ```bash
    kubectl run busybox --image=radial/busyboxplus:curl -i --tty
    ```




- Send a few requests:

    ```bash
    curl http://$IP:8888/
    ```

    

- Too much output? Filter it with jq:

    ```bash
    curl -s http://$IP:8888/ | jq .HOSTNAME
    ```

    

- Others

  ```bash
  curl http://$IP:8888/blue
  
  curl http://$IP:8888/green
  
  curl http://$IP:8888/red
  ```

  
