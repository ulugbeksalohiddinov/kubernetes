**Check running containers with _containerd_**
    
    crictl ps

**POD qaysi NODEda turganini ko'rish**

    kubectl get pods -o wide

**POD qaysi Workerda turganini ko'rish**

    kubectl get pods -n kube-system -o wide    

**NODE haqida to'liq ko'rish**

    kubectl get nodes -o wide
