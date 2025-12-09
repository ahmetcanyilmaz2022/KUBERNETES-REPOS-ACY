
1) Hazırlık — namespace ve storageclass kontrolü
# namespace oluştur
* kubectl create namespace monitoring

# cluster'ın default storage class'ını kontrol et (persistence kullanacaksan)
* kubectl get storageclass

2) Grafana Helm repo ekle ve güncelle
* helm repo add grafana https://grafana.github.io/helm-charts
* helm repo update

2) Grafana kurulumu (Helm ile)

Aşağıdaki komut, Grafana’yı Helm chart’ı kullanarak monitoring namespace’ine kurar.
Admin kullanıcı adı ve şifresini sen belirliyorsun.
Service tipi NodePort olacak — böylece localhost veya minikube/Docker Desktop üzerinden kolayca erişebilirsin.

>Test / Öğrenme için (persistence kapalı)

helm install grafana grafana/grafana \
  --namespace monitoring \
  --set adminUser=admin \
  --set adminPassword='Sifre123!' \
  --set service.type=NodePort \
  --set persistence.enabled=false

➡️ Hafif, geçici kurulum.
Pod silinirse dashboard verileri de silinir.

>Kalıcı kullanım / Prod ortamı (persistence açık)

helm install grafana grafana/grafana \
  --namespace monitoring \
  --set adminUser=admin \
  --set adminPassword='Sifre123!' \
  --set service.type=NodePort \
  --set persistence.enabled=true \
  --set persistence.storageClassName=hostpath \
  --set persistence.size=1Gi

➡️ PVC kullanır, veriler disk üzerinde saklanır.

Pod yeniden başlasa da dashboard’lar kaybolmaz.

	•	adminUser → giriş kullanıcı adı
	•	adminPassword → giriş şifresi
	•	service.type=NodePort → local erişim kolaylığı
	•	persistence.enabled=true → dashboard verileri kalıcı olur
	•	storageClassName=hostpath → senin storage class’ına uygun
	•	persistence.size=10Gi → 1GB PVC oluşturur


4) Pod ve Servis durumlarını kontrol et

* kubectl get pods -n monitoring
* kubectl get svc -n monitoring

örnek çıktı:
grafana   NodePort   10.96.184.232   <none>   80:31543/TCP   2m

Buradaki 31543 portunu not al.
Tarayıcıdan şu şekilde erişebilirsin:

>http://localhost:31543

Alternatif olarak, port-forward komutuyla da erişebilirsin:
>kubectl port-forward svc/grafana -n monitoring 3000:80
# Tarayıcı: http://localhost:3000



5) Giriş yap

Tarayıcıda açtığında kullanıcı adı ve şifre ekranı gelir:
	•	Kullanıcı: admin
	•	Şifre: Sifre123!

Eğer şifreyi unuttuysan veya default chart kurduysan:

> kubectl get secret -n monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo


###### ###### ####
# ÖNEMLİ VALUES values.yaml local yapımıza çekelim 
* helm show values grafana/grafana > my-values.yaml
# sadece values değerlerini göreyim 
* helm get values grafana -n monitoring

# valuesde değişiklik için 
a) Mevcut values’u çek:
helm get values grafana -n monitoring -o yaml > current-values.yaml
b) Dosyayı düzenle:
vim current-values.yaml
c) SMTP ayarlarını ekle:örneğin maile alert almak için ?
smtp:
  enabled: true
  host: smtp.gmail.com:587
  user: ahmetcn2022@gmail.com
  password: "GMAIL_APP_PASSWORD"
  from_address: ahmetcn2022@gmail.com
  from_name: Grafana
  skip_verify: true
d) Değişikliği uygula:
helm upgrade grafana grafana/grafana -n monitoring -f current-values.yaml
>Bu, sadece değiştirdiğin alanları günceller. Grafana pod yeniden başlar.
----------------------------------------------------------------------------------

### 1️⃣ Prometheus’u Helm ile kur

Docker Desktop Kubernetes ortamına Prometheus’u kurman gerekiyor:

* helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install prometheus prometheus-community/prometheus \
  --namespace monitoring \
  --create-namespace

Bu komut:
	•	monitoring namespace’ini oluşturur
	•	Prometheus server ve node exporter’ı kurar
	•	Otomatik olarak metrik toplamaya başlar
	•	--create-namespace → eğer namespace yoksa otomatik olarak oluşturur


2️⃣ Grafana’yı Prometheus’a bağla

Grafana’yı zaten kurmuştun, şimdi Prometheus’u “data source” olarak ekleyelim 

Yöntem 1 — Grafana UI üzerinden (kolay yöntem)
	1.	Tarayıcıdan Grafana’yı aç:
	2.	Grafana’ya giriş yap (admin / Sifre123!)
	3.	“Connections → Data Sources → Add data source” yolunu izle
	4.	“Prometheus” seç
	5.	URL kısmına şunu yaz:
  * http://prometheus-server.monitoring.svc.cluster.local
  yada sadece 
  * http://prometheus-server
  6.“Save & Test” de → ✅ “Data source is working” çıkarsa tamamdır

3️⃣ Pod metriklerini gör

Şimdi Grafana’da:
	•	“+ Create → Import” bölümüne gir
	•	Dashboard ID olarak şunu yaz: 6417 (Kubernetes Cluster Monitoring)
	•	“Prometheus” datasını seç
	•	“Import” de
    yada
    dashboard a gir sağ üst köşe new menüsü oradan import tıkla
    Dashboard ID olarak şunu yaz: 6417 (Kubernetes Cluster Monitoring)
	•	“Prometheus” datasını seç
	•	“Import” de

Bu dashboard’ta:
	•	Pod CPU / RAM kullanımı
	•	Node performansı
	•	Namespace bazlı pod grafikleri

                                                |

## FAVORİ DASHBOARD IM :)) K8S Dashboard :   15661

| **Kubernetes Cluster Monitoring (via Prome** | **6417**     | Tüm cluster genelinde izleme: node, pod, namespace vb.         |
| **Kubernetes Monitoring Dashboard**          | < **12740**  >  | Kubernetes metriklerini pod/çevre düzeyinde detaylı izleme     |
| **Kubernetes Views / Pods**                  | **15760**    | Pod seviyesinde detaylı izleme için “views” tarzı panel        |
| **Kubernetes All Pods**                      | **10676**    | Tüm pod’ları ve durumlarını hızlıca görmek için hızlı tablo tipi |
| **Kubernetes Views / Nodes List**            | **22153**    | Node listesini ve durumlarını izlemek için özel panel          |





# LİSTELE
1. Tüm yüklü Helm release’lerini göster (namespace fark etmeksizin)
helm list -A

2. Belirli bir namespace içindeki release’leri görmek için
helm list -n monitoring
3. Helm’in local cache (indirilen chart’lar) klasörünü görmek
helm env

# KALDIR

🧹1. Helm release’lerini kaldır

>helm uninstall grafana -n monitoring
>helm uninstall prometheus -n monitoring

Bu komutlar monitoring namespace’inden sadece Helm release’lerini kaldırır,
ama PVC (PersistentVolumeClaim) gibi veriler kalabilir.


2. (İsteğe bağlı) Namespace’i de sil

Eğer o namespace sadece bu iki uygulama için oluşturulduysa:

>kubectl delete namespace monitoring


3. Helm repolarını listele

Şu an ekli repoları görmek için:

>helm repo list



4. Repo’ları kaldır

Çıktıdaki isimlere göre kaldır:

>   helm repo remove grafana
>   helm repo remove prometheus-community


5. (Opsiyonel) Local cache ve metadata’yı da temizle

helm repo remove stable
rm -rf ~/.cache/helm
rm -rf ~/.config/helm
rm -rf ~/.local/share/helm




# TÜM BU KALDIRMA SİLME İŞLEMLERİNİ TEK BİR .SH FİLE İLE GERÇEKLEŞTİRİLEBİLİR 
# aynı dizinde cleanup-monitoring.sh isminde yer almaktadır 
açıklama:

Çalıştırılabilir hale getir:

chmod +x cleanup-monitoring.sh


Çalıştır:

./cleanup-monitoring.sh



-------
<aynı dizinde helm-reset.sh ile de hem prometheus grafanayı sistemden kaldırırken
Cluster içinde helm e ait tüm yapıları ns farkletmeksizin kaldırır.>
