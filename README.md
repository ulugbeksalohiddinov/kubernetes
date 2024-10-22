**Check running containers with _containerd_**
    
    crictl ps

**Pod qaysi Nodeda turganini ko'rish**

    kubectl get pods -o wide

**Pod qaysi Workerda turganini ko'rish**

    kubectl get pods -n kube-system -o wide    

    kubectl get pods -n [namespace_name] -o wide

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

**Show All Namespaces**

    kubectl get ns

**Create pod (K8Sda barcha resurslar create orqali ko'tariladi. Lekin pod run orqali ko'tariladi)**
   
     kubectl run [pod_name] --image nginx --port 80 -n [namespace_name]

**Show load nodes in K8S (analog htop)**

    kubectl top nodes

**Show load pod in K8S (analog htop)**

    kubectl top pod -n [namespace_name] [pod_name]

**Show describe pod**

    kubectl describe pod [pod_name] -n [namespace_name]

**Port-forwarding**

    kubectl port-forward pod/[pod_name] 8080:80 -n [namespace_name]

**Podni ichiga kirish**

    kubectl exec -it [pod_name] -n [namespace_name] -- sh

**Create deployment**

    kubectl create deploy [deployment_name] --image nvrbckdown/todo-list-app(docker hub image) --port 3000 --replicas 1 -n [namespace_name] --dry-run=client -o yaml > deployment.yaml

   _**Yaratilgan deployment.yaml faylni ishka tushuramiz**_

     kubectl create -f deployment.yaml
