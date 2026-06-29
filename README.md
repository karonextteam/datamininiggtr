# 📌 SenteTürk: NLP Model Optimization & Post-Training Quantization (PTQ)

### 🚀 Proje Özeti
**SenteTürk**, Türkçe Doğal Dil İşleme (NLP) modellerinde karşılaşılan yüksek bellek kullanımı ve mobil/uç cihazlardaki gecikme (latency) sorunlarını çözmek amacıyla geliştirilmiş ileri düzey bir model optimizasyon projesidir. Proje kapsamında, yüksek başarımlı yapay sinir ağı mimarileri, tahmin gücünden (zekasından) ödün verilmeden **Post-Training Quantization (PTQ)** teknikleri ile hafifletilerek uç cihazlarda çalışabilecek seviyeye getirilmiştir.

### 🎯 Problem & Çözüm
* **Problem:** Güncel NLP modellerinin devasa boyutları, yüksek RAM tüketimi ve mobil/gömülü sistemlerdeki yüksek gecikme süreleri.
* **Çözüm:** Modelin tahmin performansını maksimum düzeyde koruyarak boyutunu ve işlem yükünü dramatik şekilde hafifleten **8-bit Integer (INT8)** kuantizasyon tekniklerinin uygulanması.

---

### 📊 Veri Ön İşleme Hattı (Data Preprocessing Pipeline)
Projenin başarısının arkasında, ham veriyi gürültüden arındıran güçlü bir veri mühendisliği hattı yer almaktadır:
* **Veri Kaynağı & Hacim:** Kaggle - Turkish Sentiment Dataset üzerinde toplam **492,782 satırlık** devasa bir veri seti işlenmiştir.
* **Gürültü Azaltma:** Noktalama işaretleri, sayılar ve özel karakterler (emoji, URL vb.) gelişmiş **RegEx** filtreleriyle temizlenmiştir.
* **Metin Standardizasyonu:** Tüm metin küçük harfe dönüştürülürken Türkçeye özgü `İ/i` ve `I/ı` dönüşüm hassasiyetleri korunmuştur.
* **Morfolojik Analiz (Lemmatizasyon):** Türkçenin sondan eklemeli yapısı nedeniyle kelimeler **Zemberek** entegrasyonu ile morfolojik analizden geçirilerek köklerine indirgenmiştir.
* **Stop-Words Eliminasyonu:** "ve, veya, da, de" gibi anlamsal ağırlığı olmayan bağlaçlar veri setinden arındırılmıştır.
* **Vektörizasyon:** Temizlenen metinler Tokenizer aracılığıyla sayısal tensörlere dönüştürülerek yapay sinir ağına beslenmiştir.

---

### 🧠 Model Mimarisi & Eğitim (ANN Architecture)
* **Toplam Parametre Sayısı:** 6,467,585
* **Katman Yapısı (Katman Başına ReLU Aktivasyonu):** 
  * Giriş Katmanı -> Hidden Layer 1 (256 Nöron) -> Hidden Layer 2 (128 Nöron) -> Hidden Layer 3 (64 Nöron) -> Çıkış Katmanı (1 Nöron - Sigmoid Aktivasyonu)
* **Düzenlileştirme:** Aşırı öğrenmeyi (overfitting) engellemek adına her Dense katmanından sonra bir **Dropout** katmanı konumlandırılmıştır.
* **Hiperparametreler:** Adam Optimizer, Binary Cross-Entropy kayıp fonksiyonu, **Early Stopping** mekanizması ve 32 Batch Size ile 10 Epoch üzerinden eğitilmiştir.

---

### 📉 Post-Training Quantization (PTQ) & Performans Sonuçları
**TensorFlow Lite** kütüphanesi kullanılarak 32-bit Floating Point (FP32) model mimarisi, 8-bit Integer (INT8) formatına sıkıştırılmıştır. Elde edilen donanım ve sınıflandırma başarımları şu şekildedir:

| Metrik | Orijinal Model (FP32) | Sıkıştırılmış Model (TFLite INT8) |
| :--- | :---: | :---: |
| **Model Boyutu (MB)** | **74.1 MB** | **6.2 MB (~12 Kat Sıkıştırma)** |
| **Başarı Korunma Oranı** | %100 | **%99.967 (Sadece %0.033 Kayıp!)** |
| **ROC-AUC Skoru** | 0.9627 | 0.9623 |
| **PR-AUC Skoru** | 0.9906 | 0.9905 |

#### Hata Matrisi (Confusion Matrix) Analizi:
* **FP32 Orijinal Model:** Doğru Pozitif Sınıflandırma: %95.74 | Doğru Negatif Sınıflandırma: %79.92
* **INT8 TFLite Model:** Doğru Pozitif Sınıflandırma: %95.56 | Doğru Negatif Sınıflandırma: %80.64
* *Sonuç:* Kuantizasyon işlemi donanım yükünü ve boyutunu 12 kat azaltırken, sınıflandırma doğruluğunda neredeyse hiçbir sapmaya neden olmamıştır.

---

### 🛠️ Kullanılan Teknolojiler & Frameworkler
* Python (Ana geliştirme dili)
* TensorFlow / TensorFlow Lite (Model eğitimi ve PTQ optimizasyonu)
* Zemberek Dil Kütüphanesi (Türkçe morfolojik analiz)
* Scikit-Learn / Matplotlib / Seaborn (Metrik kıyaslama ve grafik analizleri)
