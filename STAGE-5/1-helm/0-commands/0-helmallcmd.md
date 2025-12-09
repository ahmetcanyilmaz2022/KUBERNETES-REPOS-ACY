 Helm Temel Komutlar Rehberi (Uygulamalı Versiyon)

Helm, Kubernetes üzerinde uygulamaları **paket olarak** yönetmeni sağlar.  
Aşağıdaki örneklerde `bitnami/nginx` chart’ı üzerinden ilerliyoruz.

---

## 📦 Repository İşlemleri
### 0️⃣ Helm sürümünü gör
```bash
helm version
```

### 1️⃣ Yeni repo ekle
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami

```

### 2️⃣ Repo’ları listele
```bash
helm repo list
```

### 3️⃣ Repo’yu güncelle
```bash
helm repo update
```

### 4️⃣ Chart ara
```bash
helm search repo nginx
```

### 5️⃣ Chart indir (paket olarak)
```bash
helm pull bitnami/nginx    
ls    
```

---

## Deployment İşlemleri

### 6️⃣ Chart’ı yükle
```bash
helm install my-nginx bitnami/nginx
```
> eğerki baştan conf. ayarlarını kişiselleştireceksem ;
```bash
helm install my-nginx bitnami/nginx -f values.yaml
```

### 7️⃣ Kurulu release’leri listele
```bash
helm list
```

### 8️⃣ Release durumunu kontrol et
```bash
helm status my-nginx
```

### 9️⃣ Release değerlerini gör
```bash
helm get values my-nginx
```

### 🔟 Release güncelle
```bash
helm upgrade my-nginx bitnami/nginx --set service.type=NodePort
```

### 11️⃣ Geçmişi görüntüle
```bash
helm history my-nginx
```

### 12️⃣ Geri alma (rollback)
```bash
helm rollback my-nginx 1
```

### 13️⃣ Release kaldır
```bash
helm uninstall my-nginx
```

> 💡 Alternatif:
```bash
helm upgrade --install my-nginx bitnami/nginx
```

---

##  İnceleme ve Yapılandırma

### 14️⃣ Varsayılan values dosyasını görüntüle
```bash
helm show values bitnami/nginx > values.yaml
```
> bu values yaml file yapısında yapılan değişiklikleri update etmek için ise :
```bash
helm upgrade my-nginx bitnami/nginx -f values.yaml
```
```bash
helm history my-nginx
helm rollback my-nginx 1

### 15️⃣ Release’e ait tüm detaylar
```bash
helm get all my-nginx
```

### 16️⃣ Chart’ı sadece render et (apply etmeden)
```bash
helm template bitnami/nginx
```

### 17️⃣ Chart doğrulama (lint)
```bash
helm lint mychart/
```

### 18️⃣ Chart bağımlılıklarını güncelle
```bash
helm dependency update mychart/
```

---

## 📦 Paketleme ve Versiyonlama

### 19️⃣ Chart paketle
```bash
helm package mychart/
```

### 20️⃣ Helm sürümünü gör
```bash
helm version
```

### 21️⃣ Helm ortam değişkenlerini gör
```bash
helm env
```

---

## ✨ Ek Notlar ve Temizlik

### 22️⃣ Geçmişi saklayarak kaldır
```bash
helm uninstall my-nginx --keep-history
```

### 23️⃣ Repo sil
```bash
helm repo remove bitnami
```

### 24️⃣ Helm plugin’leri listele
```bash
helm plugin list
```

### 25️⃣ Kendi chart’ını oluştur
```bash
helm create mychart
```

---

## 🎯 Adım Adım Örnek Senaryo

1. Repo ekle → `helm repo add bitnami https://charts.bitnami.com/bitnami`  
2. Repo’yu güncelle → `helm repo update`  
3. Chart ara → `helm search repo nginx`  
4. Deploy et → `helm install my-nginx bitnami/nginx`  
5. Durumu kontrol et → `helm status my-nginx`  
6. Güncelle → `helm upgrade my-nginx bitnami/nginx --set service.type=NodePort`  
7. Rollback → `helm rollback my-nginx 1`  
8. Sil → `helm uninstall my-nginx`
9. Repoyu kaldır → 'helm repo remove bitnami'
10.  helm list
11. hem repo list 

---

📘 **Hazırlayan:** DevOpsThunder > Ahmet Can YILMAZ 
💡 **Kullanım:**  
Bu rehberi `helm-komutlar.md` olarak kaydedip terminal yanında açık tutarak hızlıca komutlara ulaşabilirsin.
"""

