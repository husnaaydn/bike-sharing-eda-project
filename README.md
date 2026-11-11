# Bike Sharing EDA & Linear Regression Project

Bu proje, UCI Bike Sharing veri setindeki günlük bisiklet kiralama verileri kullanılarak hazırlanmıştır.

Projenin temel araştırma sorusu:

> **Günlük bisiklet kiralama düzeyi, hava durumu, mevsimsel ve zamansal faktörlerle birlikte nasıl değişiyor?**

Çalışma iki ana bölümden oluşmaktadır:

1. Exploratory Data Analysis (EDA)
2. Linear Regression ile günlük kiralama tahmini

---

## Veri Seti

Projede `day.csv` dosyası kullanılmıştır.

- Gözlem sayısı: **731 gün**
- Dönem: **2011-01-01 – 2012-12-31**
- Hedef değişken: `cnt`
- `cnt = casual + registered`

Veri temizleme aşamasında iki şüpheli hücre eksik değer olarak işaretlenmiştir:

- `2011-03-10` → `hum`
- `2012-08-17` → `atemp`

Hiçbir satır silinmemiş ve eksik değerler imputasyonla doldurulmamıştır.

---

## EDA Bulguları

Başlıca bulgular:

- 2012 yılındaki ortalama günlük kiralama sayısı 2011'e göre yaklaşık **%64 daha yüksektir**.
- Hava durumu kodu kötüleştikçe gözlenen günlük kiralama düzeyi azalmaktadır.
- Mevsim kodları arasında belirgin kiralama düzeyi farklılıkları görülmüştür.
- Aylık dağılımda Ocak düşük, Ocak–Haziran arasında yükselen, Haziran–Eylül döneminde yüksek kalan ve yıl sonuna doğru düşen bir desen görülmüştür.
- Haftanın günü ve çalışma günü grupları arasındaki farklar görece küçüktür.
- `cnt` ile:
  - `temp`: **r = 0.627**
  - `atemp`: **r = 0.635**
  - `hum`: **r = -0.115**
  - `windspeed`: **r = -0.235**
- `temp` ile `atemp` arasında çok yüksek korelasyon vardır:
  - **r = 0.997**
- `cnt`, `temp`, `atemp` ve `hum` yaklaşık simetrik dağılımlar gösterirken `windspeed` sağa çarpıktır.

Analiz gözlemseldir; elde edilen ilişkiler nedensellik olarak yorumlanmamıştır.

---

## Machine Learning

EDA sonrasında günlük toplam bisiklet kiralama sayısını (`cnt`) tahmin etmek için **Linear Regression** modeli kurulmuştur.

### Kullanılan Özellikler

Modelde:

- `yr`
- `holiday`
- `workingday`
- `temp`
- `hum`
- `windspeed`
- `mnth`
- `weathersit`

kullanılmıştır.

`mnth` ve `weathersit` değişkenlerine one-hot encoding uygulanmıştır.

### Kullanılmayan Özellikler

- `casual` ve `registered`: `cnt` değerini doğrudan oluşturdukları için **target leakage**
- `instant`: satır kimliği
- `dteday`: ham tarih bilgisi
- `atemp`: `temp` ile çok yüksek korelasyon nedeniyle
- `season`: `mnth` ile benzer mevsimsel bilgi taşıdığı için
- `weekday`: `holiday` ve `workingday` ile birlikte gereksiz doğrusal bağımlılığı azaltmak için

---

## Train–Test Split

Verinin zaman sırası korunmuştur.

Eksik `hum` gözlemi yalnızca model tablosundan çıkarıldıktan sonra:

- Model verisi: **730 gün**
- Eğitim: **584 gün**
- Test: **146 gün**

Tarih aralıkları:

- Train: `2011-01-01 → 2012-08-07`
- Test: `2012-08-08 → 2012-12-31`

Rastgele split yerine kronolojik split kullanılarak gelecekteki gözlemlerin eğitim verisine karışması önlenmiştir.

---

## Linear Regression Sonuçları

Test kümesinde:

| Metrik | Sonuç |
|---|---:|
| MAE | 895.12 |
| MSE | 1,486,276.25 |
| RMSE | 1219.13 |
| R² | 0.578 |

Model, test dönemindeki günlük kiralama sayısındaki varyasyonun yaklaşık **%57.8'ini** açıklamaktadır.

Gerçek ve tahmin değerleri karşılaştırıldığında model genel eğilimi takip etmekte ancak çok yüksek ve çok düşük kiralama günlerini tam olarak yakalayamamaktadır.

Residual analizinde de bazı dönemlerde sistematik fazla veya düşük tahminler görülmüştür.

---

## Model Katsayılarından Bazı Bulgular

Model katsayılarının yönleri genel olarak EDA sonuçlarıyla uyumludur:

- `temp`: pozitif
- `hum`: negatif
- `windspeed`: negatif
- `yr`: pozitif
- `holiday`: negatif

`weathersit=2` ve `weathersit=3`, referans kategori olan `weathersit=1` ile karşılaştırıldığında daha düşük tahminlerle ilişkilidir.

Katsayılar model içindeki ilişkileri göstermektedir ve nedensellik olarak yorumlanmamıştır.

---

## Proje Yapısı

```text
bike-sharing-eda-project/
├── README.md
├── requirements.txt
├── .gitignore
├── bike_sharing_project.ipynb
└── data/
    └── day.csv
```

---

## Kurulum

Gerekli paketleri yüklemek için:

```bash
pip install -r requirements.txt
```

Ardından Jupyter notebook çalıştırılabilir:

```bash
jupyter notebook
```

veya proje VS Code üzerinden açılabilir.

---

## Kullanılan Teknolojiler

- Python
- pandas
- NumPy
- Matplotlib
- Seaborn
- SciPy
- scikit-learn
- Jupyter Notebook

---

## Not

Bu proje eğitim amaçlı hazırlanmıştır. EDA ve Linear Regression sonuçları veri setindeki gözlenen ilişkileri açıklamak ve günlük bisiklet kiralama talebine yönelik temel bir tahmin modeli oluşturmak amacıyla kullanılmıştır.