**Check running containers with _containerd_**
    
    crictl ps

**Pod qaysi Nodeda turganini ko'rish**

    kubectl get pods -o wide

**Pod qaysi Workerda turganini ko'rish**

    kubectl get pods -n kube-system -o wide    

**Node haqida to'liq ko'rish**

    kubectl get nodes -o wide

**Show pod logs**

     kubectl logs [Pod_name]

  _Pod ichidagi contenir logini ko'rish_

     kubectl logs -c [contenir_name]

**K8s dagi resurs konfigini yaml faylga yo'naltirish**

     kubectl run [pod_name] --image nginx --dry-run=client -o yaml > nginx-pod.yaml

**Show API date**

      kubectl api-resources

**Create namespace**

     kubectl create ns [namespace_name]
