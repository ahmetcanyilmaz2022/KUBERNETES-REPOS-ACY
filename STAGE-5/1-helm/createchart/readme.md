
# chart oluştur
* helm create mychart

helm create mychart komutu, sıfırdan bir Helm chart iskeleti oluşturur.
Bu yapıdan basit ama öğretici bir uygulama çıkarabiliriz — örneğin bir basit Nginx web uygulaması.

mychart/
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   ├── _helpers.tpl
│   ├── hpa.yaml
│   └── tests/
│       └── test-connection.yaml
└── templates/


# basit bir nginx uygulaması oluşturmak için values yaml ı aşağıdaki şekilde güncelleyelim 

# ✅ Çalışan minimal values.yaml (nginx chart için)

replicaCount: 1            # Pod sayısı

image:
  repository: nginx        # İmaj adı
  pullPolicy: IfNotPresent # Varsa tekrar çekmez
  tag: "latest"            # Versiyon

service:
  type: NodePort           # Servis tipi
  port: 80                 # Servis portu

ingress:
  enabled: false           # Ingress kapalı

resources: {}              # Kaynak sınırları (boş)

nodeSelector: {}           # Node seçimi
tolerations: []            # Tolerans yok
affinity: {}               # Affinity yok

serviceAccount:
  create: false            # Yeni ServiceAccount oluşturma
  name: ""
  annotations: {}
  automountServiceAccountToken: true

httpRoute:
  enabled: false           # HTTPRoute kullanılmıyor

autoscaling:
  enabled: false           # HPA devre dışı
  minReplicas: 1           # Minimum pod
  maxReplicas: 3           # Maksimum pod
  targetCPUUtilizationPercentage: 80   # %80 CPU’da scale et

----------------------------------------------------------------

# chart ı deploy edelim 

>.   helm install my-nginx ./mychart

Bu komut cluster’a:
	•	Deployment
	•	Service
kaynaklarını uygular.
Pod çalışmaya başladıktan sonra kontrol et:

kubectl get pods
kubectl get svc



# tarayıcıdan test et 

kubectl get svc my-nginx-mychart

8080:31742/TCP

http://localhost:8080

# kaldırmak için 
helm uninstall my-nginx


# İstediğin zaman aşağıdaki gibi yeni parametrelerle deploy edebilirsin:

helm upgrade my-nginx ./mychart --set replicaCount=3,image.tag="alpine"