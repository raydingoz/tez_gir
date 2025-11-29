# Tez Veri Girişi & Analiz

Bu depo, tez çalışmaları için **konfigürasyon tabanlı**, tek dosyalık bir **veri girişi ve temel analiz arayüzü** içerir.  
Uygulama tamamen `veri_gir.html` dosyasının içinde çalışır; ek sunucuya, veritabanına veya framework'e ihtiyaç yoktur.  
Tüm veriler tarayıcıda tutulur ve istenildiğinde **CSV** olarak dışa aktarılabilir.

Varsayılan konfigürasyon, travma hastalarında **TBI / DAI** odaklı bir klinik çalışma için tasarlanmıştır; ancak aynı altyapı başka tezler için de kullanılabilir.

---

## Özellikler

- **Config ile yönetilen form**
  - Form alanları, başlıklar, gruplar, seçenekler ve CSV sütun adları JSON config üzerinden tanımlanır.
  - İstenirse farklı tezler için başka config dosyaları yüklenebilir.
- **Tema seçici**
  - Açık (gündüz) tema ve birden fazla koyu tema: `dark-blue`, `dark-slate`, `dark-crimson`, `dark-orange`, `dark-emerald`, `dark-violet`.
  - Seçilen tema `localStorage` içinde saklanır.
- **Hasta ID ile kayıt yönetimi**
  - Hasta / Kayıt ID alanına göre yeni kayıt oluşturma, var olan kaydı bulup güncelleme veya silme.
  - ID'yi tek tıkla panoya kopyalama.
- **Form doluluk görselleştirmesi**
  - Boş alanlar için **kırmızı çerçeve**, dolu alanlar için **yeşilimsi çerçeve** (hem input hem custom select için).
- **Custom select bileşeni**
  - Tüm tarayıcılarda tutarlı görünen, tema uyumlu, klavye ve mouse ile rahat kullanılabilen açılır liste.
- **CSV içe/dışa aktarma**
  - Mevcut verileri `AI-ready` formatta CSV olarak indirebilme.
  - Önceden oluşturulmuş bir CSV dosyasını yükleyip mevcut kayıtlarla **ID bazlı birleştirme / güncelleme**.
- **Basit istatistik modülü**
  - Sayısal değişkenler için `n`, `ortalama`, `SD`, `min`, `max`.
  - Kategorik değişkenler için frekans ve yüzde dağılımı.
- **Tamamen istemci tarafında**
  - Veriler `localStorage` üzerinde tutulur.
  - Sunucuya hasta bilgisi gönderilmez (tarayıcıdan dışa aktarılan CSV ve config dosyaları sizin kontrolünüzdedir).

---

## Dosya Yapısı

Projeyi şu temel dosyalarla kullanabilirsiniz:

- `veri_gir.html`  
  Ana uygulama dosyası. Tema seçimi, konfigürasyon yükleme, form, kayıt yönetimi, analiz ve CSV işlemleri bu dosyanın içindedir.

Opsiyonel (isteğe bağlı eklemek isterseniz):

- `tbi_dai_config.json`  
  TBI / DAI çalışmanız için örnek konfigürasyon dosyası. `veri_gir.html` içindeki yapı ile uyumludur ve **Config JSON Yükle** butonu ile içeri alınabilir.
- `tez_form_builder.html`  
  (Eğer ileride eklerseniz) Görsel arayüz ile yeni config hazırlanmasını kolaylaştıracak ayrı bir builder sayfası.

---

## Başlangıç

1. Bu depoyu klonlayın veya yalnızca `veri_gir.html` dosyasını bilgisayarınıza kaydedin.
2. Dosyayı modern bir tarayıcıda (Chrome, Edge, Firefox vb.) **double-click** ile açın  
   (statik bir HTML olduğu için ekstra sunucu gerekmez).
3. Tarayıcı, verileri `localStorage` üzerinde saklar. Aynı makinede, aynı tarayıcıda açtığınız sürece kayıtlarınız korunur.

> Not: Farklı tarayıcı veya farklı bilgisayarda çalışacaksanız, düzenli aralıklarla **CSV dışa aktarma** yapmanız önerilir.

---

## Kullanım

### 1. Tema Seçimi

Sağ üstteki **Tema** bileşeni ile:

- Otomatik (sistem tema tercihine göre)
- Gündüz (açık)
- Çeşitli gece temaları

arasından seçim yapabilirsiniz. Seçiminiz `localStorage` içinde `tez_theme_choice_v2` anahtarı ile saklanır.

---

### 2. Config (Form Tanımı) Yönetimi

Sağ kartta **Aktif Config** bölümünde:

- `Config JSON Yükle`  
  - `tez_form_builder.html` veya elle yazdığınız bir JSON config'i içeri alır.
  - Format hatalıysa uyarı gösterilir.
- `TBI Varsayılan Config`  
  - Uygulamayı TBI / DAI için hazırlanmış **varsayılan config** ile baştan kurar.

#### Config Formatı (özet)

Config kabaca şu yapıya sahiptir:

```json
{
  "projectName": "TBI DAI Çalışması",
  "storageKey": "tbi_dai_data_v2",
  "targetCount": 172,
  "fields": [
    {
      "id": "age",
      "label": "Yaş (yıl)",
      "csvLabel": "Yas",
      "type": "number",
      "group": "Demografi",
      "required": false,
      "hint": "Tam yıl cinsinden, örn. 42."
    },
    {
      "id": "sex",
      "label": "Cinsiyet (kodlu)",
      "csvLabel": "CinsiyetKod",
      "type": "select",
      "group": "Demografi",
      "required": false,
      "options": [
        { "value": "1", "label": "1 - Erkek" },
        { "value": "2", "label": "2 - Kadın" },
        { "value": "0", "label": "0 - Bilinmiyor / Diğer" }
      ]
    }
    // ...
  ]
}
```

- `projectName`  
  Arayüzde görünen proje adı ve CSV dosya adında kullanılır.
- `storageKey`  
  `localStorage` içinde kullanılacak anahtar; her proje için benzersiz olması önerilir.
- `targetCount`  
  Hedef hasta sayısı (ör. 172). Progress bar bu sayıya göre hesaplanır.
- `fields`  
  Formun alan tanımları. Her alan:
  - `id`: Dahili alan adı (JSON ve kod tarafında kullanılan key).
  - `label`: Formda görünen başlık.
  - `csvLabel`: CSV başlığında kullanılacak sütun adı.
  - `type`: `number` veya `select` (gerekirse `text` de eklenebilir).
  - `group`: Formdaki grup başlığı (Demografi, Klinik, Lab, Görüntüleme vb.).
  - `hint`: (Opsiyonel) Etiket altındaki kısa açıklama.
  - `options`: `select` tipindeki alanlar için değer–etiket listesi.

---

### 3. Veri Girişi Akışı

Sol kartta, üst kısımda:

1. **Hasta ID alanına** (örn. `TBI-001`, PACS ID veya protokol numarası) yazın.
2. Eğer daha önce girilmiş bir ID ise:
   - `Ara / Yükle` butonuna basıldığında form ilgili kayıtla doldurulur.
3. Yeni kayıt başlatmak için:
   - ID yazdıktan sonra `Yeni Kayıt` butonuna tıklayın; form boşaltılır ve bu ID'ye ait yeni kayıt oluşturulur.

Formda:

- Boş alanlar: kırmızı tonlarında çerçeve ve glow (tamamlanmamış).  
- Dolu alanlar: yeşil tonlarında çerçeve ve glow (tamamlanmış).

Kayıt işlemleri:

- `Kaydet / Güncelle`:  
  - İlgili **ID için yeni kayıt ekler** veya mevcut kaydı **günceller**.
- `Formu Temizle`:  
  - ID haricindeki alanları temizler (ID sabit kalır).
- `Kaydı Sil`:  
  - Geçerli ID'ye ait kaydı tümüyle siler.
- `ID'yi Kopyala`:  
  - Mevcut ID'yi panoya kopyalar (clipboard API desteklenmiyorsa uyarı verir).

Tüm kayıtlar, aktif config içindeki `storageKey` değerine göre `localStorage` altında saklanır.

---

### 4. CSV İçe / Dışa Aktarma

Sağ kartta:

- `CSV Dışa Aktar`
  - Tüm kayıtları kolon başlıkları ile birlikte CSV dosyası olarak indirir.
  - Sütun başlıkları:
    - İlk sütun: `HastaID`
    - Diğer sütunlar: config içindeki `csvLabel` değerleri.
- `CSV Yükle`
  - Daha önce bu arayüzden çıkarılmış veya aynı şemaya uyacak şekilde oluşturulmuş CSV dosyasını okur.
  - `HastaID` üzerinden eşleştirme yaparak:
    - Var olan kaydı günceller veya
    - Yoksa yeni kayıt ekler.
  - Satır sayısı kadar **eklenen / güncellenen kayıt** sayısını özetler.

> İpucu: Aynı config ile çalışan iki farklı bilgisayardaki verileri tek CSV üzerinden birleştirmek mümkün. Ancak çakışan ID'lerde son yüklenen dosyanın satırı baskın olacaktır.

---

### 5. Temel Analizler

Sağ kartın ortasında yer alan iki analiz paneli:

#### Sayısal Alanlar – Özet

- Config'te `type: "number"` olan alanlar için:
  - Gözlem sayısı (`n`)
  - Ortalama (`Ort.`)
  - Standart sapma (`SD`)
  - Minimum (`Min`)
  - Maksimum (`Max`)

Bu özet, veri toplama sürecinde **mantık kontrolü** ve kabaca dağılım görmek için kullanılabilir  
(örneğin GKS, yaş, Na, glukoz, laktat, vs.).

#### Kategorik Alanlar – Dağılım

- `type: "select"` olan alanlar için:
  - Her kategori değeri için `n` ve `%` bilgisi
  - Etiketler, config'teki `options` dizisinden alınır.

---

## Varsayılan TBI / DAI Config Hakkında

Varsayılan config, literatürde sık kullanılan değişkenlere göre kabaca şu başlıkları içerir:

- **Demografi**
  - Yaş
  - Cinsiyet (kodlanmış)
- **Outcome**
  - GOS 6. ay
  - GOSE 6. ay
- **Klinik**
  - GKS (ilk / en kötü)
  - Pupilla reaktivitesi
  - Entübasyon durumu
  - ED hipotansiyon / hipoksi varlığı
  - Travma mekanizması (AİTK, ADTK, düşme, diğer)
- **Görüntüleme**
  - SWI DAI skoru
  - Midline shift
  - Bazal sistern durumu
  - EDH / SDH / travmatik SAH / kontüzyon varlığı
- **Lab**
  - Na, glukoz, laktat
  - Hb, trombosit, INR, kreatinin

Bu yapı, hem klasik prognostik modeller (IMPACT / CRASH benzeri) hem de SWI tabanlı DAI skorlaması ile  
AI / makine öğrenmesi modellerinde **özellik seti** olarak kullanılabilecek bir şema sağlar.

---

## Gizlilik ve Güvenlik Notları

- Uygulama, klinik verileri **sunucuya göndermez**; tüm veriler tarayıcıda tutulur.
- Tarayıcı önbelleği temizlenirse veya `localStorage` silinirse veriler kalıcı olarak kaybolur.
- Tez çalışmaları için:
  - Verileri **başta anonimleştirmek**,
  - Mümkünse yalnızca sahte / kodlanmış ID kullanmak ve
  - Düzenli aralıklarla CSV yedek almak önerilir.

---
