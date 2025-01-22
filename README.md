**Check running containers with _containerd_**
    
    crictl ps

**Pod qaysi Nodeda turganini ko'rish**

    kubectl get pods -o wide

**Describe pods**

    kubectl describe pods -n [namespace]

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

**Podni ichidan turip boshqa bir nodega ping berish**

- Misol uchun: busybox pod yaratvolamiz.
  
**    kubectl run busybox --image busybox --command -- sleep 3600**

- Keyin ping beramiz

    kubectl busybox -- ping x.x.x.x[node_ip]
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

 **Stop port-forwarding**

    pkill -f "kubectl port-forward [pod_name] 8080:80"  

**Podni ichiga kirish**

    kubectl exec -it [pod_name] -n [namespace_name] -- sh

**Copy files to/from containers running in the pod** 

    kubectl cp --container=kuard /etc/os-release kuard:/tmp/

**Create deployment**

    kubectl create deploy [deployment_name] --image nvrbckdown/todo-list-app(docker hub image) --port 3000 --replicas 1 -n [namespace_name] --dry-run=client -o yaml > deployment.yaml

   _**Yaratilgan deployment.yaml faylni ishka tushuramiz**_

     kubectl create -f deployment.yaml

  _**Agar deployment.yaml faylni ichini o'zgartirilsa. Misol uchun replicas: ni sonini oshirilsa applay qilip qayta ishka tushuriladi**_

      kubectl apply -f deployment.yaml   

**Show replica set**

    kubectl get rs -n [namespace_name]

**Hamma NAMESPACElardagi podlarni ko'rish**

    kubectl get pods --all-namespaces

**K8S clusterdagi ishlamayaotgan podlarni ko'rish**

    kubectl get pods --all-namespaces | grep -v "Running"

**Deleate deployment**

    kubectl deleate -f deployment.yaml  

**Show deployment**

    kubectl get deploy

**To see the Deployment rollout status**

    kubectl rollout status deployment/nginx-deployment

**Deploymentni ichidagi podning imageni yangilash**

    kubectl set image  deployments/nginx-deployment nginx=nginx:1.9.1

**Deploymentni ichidagi podlar konfiguratsiyasida qandaydur o'zgarish bo'lsa va _o'zgarishni avvalgi holatiga qaytarish_**

    kubectl rollout undo deployments/nginx-deployment
    
       - out: deployment.extensions/nginx-deployment rolled back

**Deploymentni statusini tekshirish**

    kubectl rollout status deployments/nginx-deployment 

	- out: deployment "nginx-deployment" successfully rolled out

 
**Namespacedagi Deploymentga restart berish**

	kubectl rollout restart deployment --namespace=<your-namespace>
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
        kubectl expose deploy nginx --port 80 --target-port 80 --type ClusterIP -n NAMESPACE --dry-run=client -o yaml > nginx.yaml

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

    kubectl create ingress [ingress-name] --rule "domain_name/=service_name:service_port -n NAMESPACE --dry-run client -o yaml"

EXAMPLE:

    kubectl create ingress ingress-nginx --rule "kubs.uz/=nginx:80" -n aaa --dry-run=client -o yaml > nginx-ingress.yaml


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
    - Exact qilib /test qilinsa faqat /test ga javob beradi. Hatto testni orasiga nimadur qo'shilsa ham javob bermaydi
               
**Show ingress**

    kubectl get ingress

**Show endpoints**

    kubectl get endpoints

**Apply yaratolmaydi faqat bor narsani update qilip ishka tushiradi. Create yo'q narsani yaratadi va ishka tushiradi.**

---------------------------------------------------------------------------------------------------------------------------------
**Deployment faylga .env qo'shish Bu usul to'g'ridan to'g'ri deployment faylni ichiga envlarni qo'ship qo'yish:**

    containers:
        env:
        - name: ENV
          value: "Development"
        - name: "AUTHOR"
          value: "Ulugbek"

**Configmap K8s. Bu usulda kubernetisni ichida configmap fayl yaratiladi va u deployment faylda chaqirib olinadi**

    kubectl create configmap environment[ConfigMap_NAME] --from-literal ENV=Develooment --from-literla AUTHOR=Ulugbek --dry-run=lient -o yaml > environment-configmap.yaml

  _**Keyin yasab olingan configmap faylni deployment resursiga chaqirip olinadi**_

       containers:
         env:
         - name: ENV
           valueFrom:
             configMapKeyRef:
               name: environment
               key: ENV
         - name: AUTHOR
           valueFrom:
             configMapKeyRef:
               name: environment
               key: AUTHOR 

   _Agar configmapdan envlarni o'zgartirsan. Podni deleate qilip keyin yana ko'tarish kerak._

**Secret configmap yaratish. Envlarni shifirlangan ko'rinishda saqlaydi (base64 yordamida)**

    kubectl create secret generic secret-env[SECRET_NAME] --from-literal DB_HOST=127.0.0.1 --from-literla DB_PORT=5432 --dry-run=client -o yaml > secret-env.yaml

 _**Keyin yasab olingan secretconfigmap faylni deployment resursiga chaqirip olinadi**_

       containers:
         env:
         - name: ENV
           valueFrom:
             secretKeyRef:
               name: secret-env
               key: ENV
         - name: AUTHOR
           valueFrom:
             secretKeyRef:
               name: secret-env
               key: AUTHOR 

**Yasalgan podni json faylidan envlarni tortish**

    - name: POD
      valueFrom: 
        fieldRef:
          fieldPath: metadate.name

**Yasalgan nodeni json faylidan envlarni tortish**

    - name: NODE
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName

----------------------------------------------------------------------------------------------------------------------

**Resources and limits in K8S**

_**Deployment resurs fayl yaratip olinadi va uni ichiga resurslar qo'sgiladi.Limits requestsdan kotta yoki teng bo'lishi kerak. Bo'lmasam xatolik beradi**_

    containers:
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"

----------------------------------------------------------------------------------------------------------------------------

**Scheduling in K8S**

_Schedulingga javob beruvchi asosiy komponent bu - kube-scheduler. U podlarni qaysi nodega o'rnatilishini hal qiladi_

_**Scheduling qilishini bir necha usullari mavjud:**_

**- nodeName - eng oddiy usuli, deployment faylga qaysi nodeda turishi yozib qo'yiladi**

    spec:
      nodeName: node1

_**- nodeSelector - bu yerda labelar orqali nodelarga podlar o'rnatiladi. 1- label resurs yaratiladi va keyin u deployment faylga chaqiriladi. Label yaratilayotganda 1 ta nodega 1 nechta labelarni e'lon qilsa bo'ladi. Maqsad 1 ta nodega bir nechta podlarni qo'yish uchun.**_

**Create label for node**

    kubectl label node node3[node_name] familiya=salohiddinov ismi=ulugbek[key=value]

**Show labels**

    kubectl get nodes --show-labels

**Add label in deployment file**

    spec:
      nodeSelector:
        familiya: salohiddinov
        ismi: ulugbek

_Label ichida nechta key - valuelar bo'lsa hammasini deployment faylga qo'shilmasa pod ko'tarilmaydi. Yani Hamma key valuelar bo'lishi kerak.( ? tekshirip ko'rish kerak)_

**Create label for node**

    kubectl label node node3 ulugbek=salohiddinov  -n NAMESPACE

**Add affinity in deployment file**

    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: ulugbek
                operator: In
                values:
                - salohiddinov
                
                
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: another-node-label-key
                operator: In
                values:
                - another-node-label-value

**requiredDuringSchedulingIgnoredDuringExecution** - bu nodeSelectorga o'xshab labelar bor yoki yo'qligini tekshiradi. Bu holatda label bo'sagina qo'yadi. Bo'masa qo'ymidi Pandiningga tuship qoladi. Bu ishlap turgan podga nisbatan ishlamaydi. Faqat pod ko'tarilayotgan vaqtda ishlaydi.

**preferredDuringSchedulingIgnoredDuringExecution** - bu agar labelarni topa olmasa o'zi resurslardan foydalangan holada boshqa nodelarga qo'yishi mumkun. Bu ham ishlap turgan podga nisbatan ishlamaydi. Faqat pod ko'tarilayotgan vaqtda ishlaydi.

**operator: In** - bu deployment faylni keylariga 1 nechta valuelar yozilgan bo'lsa yo uni yo buni bo'lishi kerak degani. Hamma valuelar to'g'ri kelishi kerak deb majburlamaydi. 

**operator: NotIn** -bu operatorda unisiyam bo'masligi kerak bunisi ham bo'lmasligi kerak. Deployment fayldagi hech qaysisi bo'lmasligi kerak

**operator: Exists** - bu operatorda value berilmaydi. Keyni o'zini topsa bo'ldi

**operator: DoesNotExists** - bu operatorda Deployment fayldagi ko'rsatilgan key bo'maganiga qo'yadi


**-- podAffinity --**

    apiVersion: v1
    kind: Pod
    metadata:
      name: with-pod-affinity
    spec:
      affinity:
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: security
                operator: In
                values:
                - S1
            topologyKey: topology.kubernetes.io/zone
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                - key: security
                  operator: In
                  values:
                  - S2
              topologyKey: topology.kubernetes.io/zone
      containers:
      - name: with-pod-affinity
        image: registry.k8s.io/pause:2.0apiVersion: v1      

**Pod qayerda turgan bo'lsa usha podga nisbatan ishlanadi.**

- **podAffinit**: Bu yerda labellar mos kelsa pod shu pod turgan nodega tushishi mumkun.
- **podAntiAffinity**: Bu yerda labellar mos kelsa pod shu pod turgan nodega tushmaydi.



**-- nodeAffinity --**

    kind: Pod
    metadata:
      name: with-node-affinity
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: topology.kubernetes.io/zone
                operator: In
                values:
                - antarctica-east1
                - antarctica-west1
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            preference:
              matchExpressions:
              - key: another-node-label-key
                operator: In
                values:
                - another-node-label-value
      containers:
      - name: with-node-affinity
        image: registry.k8s.io/pause:2.0

Bu nodeSelectorga o'xshap ishlaydi. Faqat sintaksisi aniqroq. Key va valuelari mos kelsa nodega qo'yadi operator: In bilan ishlatilsa.

Agar operator: NotIn bilan ishlatilsa teskarisi bo'ladi

?

?

?

---------------------------------------------------------------------------------------------------------------------------------------------

**Cronjob, Job**

- Cronjob jobni yaratadi, job esa podni yaratadi

**Cronjob manifest file**

    apiVersion: batch/v1
    kind: CronJob
    metadata:
      name: hello
    spec:
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: hello
                image: busybox:1.28
                imagePullPolicy: IfNotPresent
                command:
                - /bin/sh
                - -c
                - date; echo Hello from the Kubernetes cluster
              restartPolicy: OnFailure

**Show cronjob**

    kubectl get cj(cronjob)

**Show job**

    kubectl get job

**Manifet fayl orqali cronjobni o'chirish**

    kubectl delete -f [cronjob-manifestfile-name.yaml]
---------------------------------------------------------------------------------------------------------------------------------

**Prob**

_**-liveness probes**_ - bu containerni ko'tarilganini tekshiradi. Ishlavotimi yo'qmi.

_**-readiness probes**_ - bu trafikni borip kela olishini tekshiradi. Zapros berilganda javob qaytvotimi yo'qmi shuni tekshiradi

**Liveness probes**

registry.k8s.io/busybox image orqali konteynerni boshqaradigan Pod yaratatilishiga misol:

    apiVersion: v1
    kind: Pod
    metadata:
      labels:
        test: liveness
      name: liveness-http
    spec:
      containers:
      - name: liveness
        image: registry.k8s.io/e2e-test-images/agnhost:2.40
        args:
        - liveness
        livenessProbe:
          httpGet:
            path: /healthz
            port: 8080
            httpHeaders:
            - name: Custom-Header
              value: Awesome
          initialDelaySeconds: 5
          periodSeconds: 5

periodSeconds - kubelet har 5 soniyada trikiligini[liveness prod] tekshirish kerakligini bildiradi. initialDelaySeconds - kubletga tiriklikni tekshirishdan oldin 5 soniya kutish kerakligini etadi. Tiriklikni tekshirish uchun kubelet konteynerda ishlayotgan va 8080-portda tinglayotgan serverga HTTP GET so‘rovini yuboradi. Agar so'rov berilganda serverni /healthz yo‘liga  muvaffaqiyatli kodni qaytarsa, kubelet konteynerni tirik va sog'lom deb hisoblaydi. Agar ishlov beruvchi xato kodini qaytarsa, kubelet konteynerni o'ldiradi va uni qayta ishga tushiradi. 200 dan katta yoki unga teng va 400 dan kam bo'lgan har qanday kod muvaffaqiyatni ko'rsatadi. Boshqa har qanday kod muvaffaqiyatsizlikni ko'rsatadi.

**Show describe pod liveness-exec**

    kubectl describe pod liveness-http

**Show pod liveness-exec**

    kubectl get pod liveness-http

**Readiness probes**

    readinessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5


**Deployment manifest fayl uchun liveness va readiness prob**

    containers:
      - image: nvrbckdown/flask-app
        name: flask-app
	  livenessProbe:
	    httpGet:
	      path: /env
		  port: 5000
	    initialDelaySeconds: 10
	    periodSeconds: 1
	  readinessProbe:
	    tcpSocket:
	      port: 5000
	    initialDelaySeconds: 15
	    periodSeconds: 10
-------------------------------------------------------------------------------------------------------------------------------
    
**RBAC**

**Create service account**

    kubectl create serviceaccount NAME-Serviceaccount -n ulugbek 
    yoki
    kubectl create sa NAME-Serviceaccount -n ulugbek 

**Show Serviceaccounts**

    kubectl get sa -n ulugbek

**RBACni 2 ta turi mavjud:**

_**- role**_ -  bu pod darajasida qandaydur accesslar berish

_**- Clusterrole**_ - bu umumiy cluster darajasida qandaydur accesslar berish

Shu turlar asosida Binding yasaladi.

**Create Role** 

	kubectl create role test-role[name] --verb=get --verb=list --resource=pod -n ulugbek --dry-run=client -o yaml > test-role.yaml

verb= "get", "list", "watch", "create", "update", "patch", "delete"
 	
  	apiVersion: rbac.authorization.k8s.io/v1
	kind: Role
	metadata:
  	  creationTimestamp: null
  	  name: test-role
  	  namespace: ulugbek
  	rules:
	- apiGroups:
  	  - "" #bu api v1 ga teng bo'sh maydon
  	  resources:
  	  - pods
  	  verbs:
  	  - get
          - list

**Show role**

	kubectl get role -n ulugbek


**Create RoleBinding**

	kubectl create rolebinding [rolebinding-NAME] --role=[role-NAME] --serviceaccount=[NAMESPACE]:[Serviceaccount-NAME] -n ulugbek --dry-run=client -o yaml > test-rolebinding.yaml

Example:

 	kubectl create rolebinding developer --role=test-role --serviceaccount=ulugbek:test -n ulugbek --dry-run=client -o yaml > test-rolebinding.yaml

**Show rolebinding**

	kubectl get rolebinding -n ulugbek

 **Create ClusterRole**

	kubectl create clusterrole test-clusterrole --verb=get --verb=list --resource=secrets -n ulugbek --dry-run=client -o yaml > test-clusterrole.yaml

   	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRole
	metadata:
  	  creationTimestamp: null
  	  name: test-clusterrole
     	  namespace: ulugbek
	rules:
	- apiGroups:
  	  - ""
          resources:
  	  - secrets
  	  verbs:
  	  - get
  	  - list

**Agar clusterdagi barcha resourcelarga to'liq access bermoqchi bo'linsa**

	apiVersion: rbac.authorization.k8s.io/v1
	kind: ClusterRole
	metadata:
  	  creationTimestamp: null
  	  name: test-clusterrole
     	  namespace: ulugbek
	rules:
	- apiGroups: ["*"] # barcha api-resourcelarga
  	  resources: ["*"] # barcha resurslarga
  	  verbs: ["*"] # barcha narsa qilishga "get", "list", "watch", "create", "update", "patch", "delete"
	

**Create ClusterRoleBinding**

 	kubectl create clusterrolebinding [clusterrolebinding-NAME] --clusterrole=[clusterrole-NAME] --serviceaccount=[NAMESPACE]:[Serviceaccount-NAME] -n ulugbek --dry-run=client -o yaml > test-clusterrolebinding.yaml

Example:

	kubectl create clusterrolebinding devops --clusterrole=test-clusterrole --serviceaccount=ulugbek:test -n ulugbek --dry-run=client -o yaml > test-clusterrolebinding.yaml

**Show Clusterrolebinding**

	kubectl get clusterrolebinding -n ulugbek
