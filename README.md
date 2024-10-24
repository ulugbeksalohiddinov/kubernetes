**Check running containers with _containerd_**
    
    crictl ps

**Pod qaysi Nodeda turganini ko'rish**

    kubectl get pods -o wide

**Pod qaysi Workerda turganini ko'rish (kube-system namespaceida turgan podlarni ko'rilmoqda)**

    kubectl get pods -n kube-system -o wide    

    kubectl get pods -n [namespace_name] -o wide

**Node haqida to'liq ko'rish**

    kubectl get nodes -o wide

**Show pod logs**

     kubectl logs [Pod_name] -n [namespace_name]

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
-------------------------------------------------------------------------------------------------------------------------------------------------------------

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

  _**Agar deployment.yaml faylni ichini o'zgartirilsa. Misol uchun replicas: ni sonini oshirilsa applay qilip qayta ishka tushuriladi**_

      kubectl apply -f deployment.yaml   

**Show replica set**

    kubectl get rs -n [namespace_name]

**Hamma NAMESPACElardagi podlarni ko'rish**

    kubectl get pods --all namespaces

**K8S clusterdagi ishlamayaotgan podlarni ko'rish**

    kubectl get pods --all-namespaces | grep -v "Running"

**Deleate deployment**

    kubectl deleate -f deployment.yaml  

**Show deployment**

    kubectl get deploy
-----------------------------------------------------------------------------------------------------------------------------------------------------------

**Show daemonset**

    kubectl get ds

**Show iptables**

    iptables -vL -t nat

**Service type**

    ClusteIP - bu podlar o'rtasida ma'lumot almashuvchi service. Misol uchun 1 ta poddan 2 podni nomi bo'yicha zapros berish 
    NodePort - bu podlarga nodeni IP sini portiga tashqaridan dostup ochadi 
    LoadBalancer
    Headless

**Serviceni _expose_ orqali yaratiladi. Yani biror yo'nalishga kommunikatsiya ochib ber digani**

  **ClusteIP**
  
    kubectl expose deployment [deployment_name] --port [service_port] --target-port [container_port] --dry-run=client -o yaml > service.yaml    

    **Example**

        kubectl create deploy nginx --image nginx --replicas 1 -n NAMESPACE
        kubectl expose deploy nginx --port 80 --target-port 80 -type ClusterIP -n NAMESPACE

        kubectl create deploy nginx --image nginx --replicas 1 -n NAMESPACE --dry-run=client -o yaml > nginx.yaml
        kubectl expose deploy nginx --port 80 --target-port 80 -type ClusterIP -n NAMESPACE --dry-run=client -o yaml > nginx.yaml

        kubectl apply yoki create -f nginx.yaml

  **NodePort**

    **Example**

        kubectl create deploy nginx --image nginx --replicas 1 -n NAMESPACE
        kubectl expose deploy nginx --port 80 --target-port 80 -type NodePort -n NAMESPACE

        kubectl create deploy nginx --image nginx --replicas 1 -n NAMESPACE --dry-run=client -o yaml > nginx.yaml
        kubectl expose deploy nginx --port 80 --target-port 80 -type NodePort -n NAMESPACE --dry-run=client -o yaml > nginx.yaml
        
     
 **Show Service**

     kubectl get svc

----------------------------------------------------------------------------------------------------------------------------------------------------------
**Ingress (doamin_address) --> service --> Pod**

_- Ingress yaratish uchun 1 deployment yaratish kerak 2 service yaratish kerak va ular asosida ingress yaratiladi_

**Create Ingress**

    kubectl create ingress [ingress_name] --rule "domain_name/=service_name:service_port"

_Bu yerda ingress ko'tarildi lekin uni klassi yo'q(nginx yo'q). U tushumaydi nginx controllerni ishlatishi kerakligini._

   _Buni to'g'irlash uchun_
   
     kubectl edit ingress [ingress_name]
    
        - metadata:  dan keyin annotations qo'shiladi

            annotations:
               kubernetes.io/ingress.class: nginx[controller_name]

        - ma'lum bir vaqtdan keyin address qismida IP addres oladi. Bu IPni servicedagi ClusterIPdan olib keladi.   

    Keyin ingressga Domen qo'shamiz. kubectl edit ingress [ingress_name] orqali

        spec:
          rules:
          - host: domain_name

    Ingress config ichidagi pathType qismi bo'lib, uni 2 turi mavjud. Exact yoki Prefix     

    - Prefix qilib / qilinsa rootdan keyingi barcha linklar zaproslarga javob beradi
    - Exact qilib /test qilinsa faqat /test ga javob beradi. Hatto testni orasiga nimadur qo'shilsa ha javob bermaydi
               
**Show ingress**

    kubectl get ingress
