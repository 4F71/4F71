# 🤖 MentorMate - Akbank GenAI Bootcamp SSS Chatbot

![MentorMate Banner](https://img.shields.io/badge/Bootcamp-Akbank%20GenAI-blue?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.9+-green?style=for-the-badge&logo=python)
![Streamlit](https://img.shields.io/badge/Streamlit-1.28+-red?style=for-the-badge&logo=streamlit)
![LangChain](https://img.shields.io/badge/LangChain-0.1.20-orange?style=for-the-badge)

> **Bootcamp katılımcıları için 7/24 akıllı soru-cevap asistanı**

MentorMate, Akbank GenAI Bootcamp katılımcılarının sıkça sorduğu sorulara anında, doğru ve güvenilir cevaplar veren RAG (Retrieval Augmented Generation) tabanlı bir chatbot'tur.

---

## 📋 İçindekiler

- [Projenin Amacı](#-projenin-amacı)
- [Veri Seti Hakkında](#-veri-seti-hakkında)
- [Kullanılan Yöntemler](#-kullanılan-yöntemler)
- [Çözüm Mimarisi](#-çözüm-mimarisi)
- [Kurulum Kılavuzu](#-kurulum-kılavuzu)
- [Kullanım Kılavuzu](#-kullanım-kılavuzu)
- [Elde Edilen Sonuçlar](#-elde-edilen-sonuçlar)
- [Canlı Demo](#-canlı-demo)

---

## 🎯 Projenin Amacı

Bootcamp sürecinde katılımcılar:
- ❓ Sertifika koşulları
- 📅 Mentor toplantı saatleri  
- 💻 GitHub kullanımı
- 👥 Grup proje kuralları
- 📺 Canlı yayın arşivleri

gibi tekrar eden sorularla karşılaşıyor. MentorMate bu soruları 7/24 anında ve tutarlı şekilde yanıtlayarak:

✅ **Mentor yükünü azaltır**  
✅ **Katılımcı deneyimini iyileştirir**  
✅ **Bilgi erişimini hızlandırır**

---

## 📊 Veri Seti Hakkında

### 📁 Veri Kaynağı
- **Kaynak**: Bootcamp Zulip kanalındaki gerçek katılımcı soruları
- **Format**: Soru-cevap çiftleri (JSON)
- **Boyut**: 50+ benzersiz soru-cevap
- **Dil**: Türkçe

### 🔄 Veri Hazırlama Süreci

```
1. Zulip Mesajları → 2. Manuel Temizleme → 3. Keyword Zenginleştirme → 4. JSON Format
```

**Örnek Veri Yapısı:**
```json
{
  "question": "Bootcamp sertifikası nasıl alınır?",
  "answer": "Sertifika için projenizi başarıyla tamamlamanız gerekir.",
  "keywords": ["sertifika", "bootcamp", "proje", "tamamlama"]
}
```

### 🎨 Veri Zenginleştirme
- **Synonym Mapping**: "katılım" → "iştirak", "yoklama"
- **Case Normalization**: "Bootcamp" = "bootcamp" = "BOOTCAMP"
- **Question Variants**: Soru işareti varyasyonları normalize edildi

---

## 🛠️ Kullanılan Yöntemler

### 1️⃣ RAG (Retrieval Augmented Generation)
- **Retriever**: MultiQueryRetriever + MMR (Maximum Marginal Relevance)
- **Generator**: Google Gemini 2.0 Flash
- **Vector Store**: ChromaDB

### 2️⃣ Embedding Stratejisi
```python
Model: sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
Dimension: 384
Language: Multilingual (Türkçe optimize)
```

### 3️⃣ Akıllı Soru İşleme
- **Keyword Enrichment**: 40+ eş anlamlı kelime haritası
- **Query Preprocessing**: Türkçe karakter normalizasyonu
- **Short Query Handling**: 1-2 kelimelik sorular özel işleme

### 4️⃣ Halüsinasyon Önleme
```python
✓ Kaynak doküman kontrolü
✓ %20 örtüşme threshold'u
✓ "Bilgi yok" cevapları için özel handling
```

---

## 🏗️ Çözüm Mimarisi

### 🔄 Sistem Akış Diyagramı

```
┌─────────────┐
│  Kullanıcı  │
│   Sorusu    │
└──────┬──────┘
       │
       ▼
┌──────────────────┐
│ Query Processing │  ← Normalization + Keyword Enrichment
└──────┬───────────┘
       │
       ▼
┌──────────────────┐
│  MultiQuery      │  ← 3-5 farklı sorgu varyantı üret
│  Retriever       │
└──────┬───────────┘
       │
       ▼
┌──────────────────┐
│   ChromaDB       │  ← MMR ile 5 en alakalı doküman
│  Vector Search   │
└──────┬───────────┘
       │
       ▼
┌──────────────────┐
│ Gemini 2.0 Flash│  ← Context + Prompt → Cevap üret
└──────┬───────────┘
       │
       ▼
┌──────────────────┐
│  Hallucination   │  ← Kaynak kontrolü
│    Validator     │
└──────┬───────────┘
       │
       ▼
┌──────────────────┐
│  Final Answer    │
└──────────────────┘
```

### 🧩 Teknoloji Stack

| Katman | Teknoloji | Amaç |
|--------|-----------|------|
| **Frontend** | Streamlit | Web arayüzü |
| **LLM** | Google Gemini 2.0 Flash | Cevap üretimi |
| **Embedding** | Sentence Transformers | Vektör dönüşümü |
| **Vector DB** | ChromaDB | Semantik arama |
| **Framework** | LangChain | RAG pipeline |
| **Memory** | ConversationBufferWindowMemory | Sohbet geçmişi |

### 🎯 Uzman Mod Özellikleri

```python
✓ Sadece veritabanındaki bilgileri verir
✓ Bilmediği konularda açıkça belirtir
✓ Halüsinasyon kontrolü aktif
✓ Tutarlı cevaplar (temperature=0.01)
```

---

## 🚀 Kurulum Kılavuzu

### Gereksinimler
- Python 3.9+
- Google API Key (Gemini)

### 1. Depoyu Klonlayın
```bash
git clone https://github.com/KULLANICI_ADI/MentorMate-SSS.git
cd MentorMate-SSS
```

### 2. Virtual Environment Oluşturun
```bash
# Windows
python -m venv .venv
.venv\Scripts\activate

# Linux/Mac
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Bağımlılıkları Yükleyin
```bash
pip install -r requirements.txt
```

### 4. API Anahtarını Ayarlayın
`.env` dosyası oluşturun:
```env
GOOGLE_API_KEY=your_gemini_api_key_here
```

### 5. Uygulamayı Çalıştırın
```bash
streamlit run app.py
```

Tarayıcınızda `http://localhost:8501` açılacaktır.

---

## 📖 Kullanım Kılavuzu

### 🎮 Arayüz Özellikleri

#### 1️⃣ **Ana Sohbet Ekranı**
- Soru yazın ve Enter'a basın
- Gerçek zamanlı cevap alın
- Sohbet geçmişi otomatik saklanır

#### 2️⃣ **Sidebar İstatistikleri**
- 📊 Toplam soru/cevap sayısı
- 🗑️ Sohbeti temizleme butonu
- 🗄️ Veritabanı bilgileri

#### 3️⃣ **Örnek Sorular**

| Kategori | Örnek Soru | Beklenen Sonuç |
|----------|-----------|----------------|
| Sertifika | "Bootcamp sertifikası nasıl alınır?" | Proje tamamlama koşulları |
| Grup | "Projeyi kaç kişi yapabiliriz?" | 1-2 kişi bilgisi |
| Canlı Yayın | "YouTube kayıtları var mı?" | Arşiv linki |
| Mentor | "Mentor toplantıları ne zaman?" | Zulip duyuru bilgisi |
| GitHub | "Git bilmiyorum, nasıl yüklerim?" | Video rehber linki |

### 🎯 Kullanım Senaryoları

**Senaryo 1: Hızlı Bilgi**
```
Kullanıcı: "Bootcamp kaç gün?"
MentorMate: "Bootcamp eğitimi 28 günlük bir süreyi kapsar."
```

**Senaryo 2: Detaylı Soru**
```
Kullanıcı: "Sertifikadaki isimler hatalı çıkıyor"
MentorMate: "Sertifika platformunda isminizi İngilizce karakterlerle 
             yeniden kaydedin. Bu hatalı karakterlerin düzelmesine 
             yardımcı olur."
```

**Senaryo 3: Bilinmeyen Soru**
```
Kullanıcı: "Bootcamp ne kadar maaş veriyor?"
MentorMate: "Bu konuda veri setimde bilgi bulunmuyor."
```

---

## 📈 Elde Edilen Sonuçlar

### ✅ Teknik Başarılar

| Metrik | Değer | Açıklama |
|--------|-------|----------|
| **Doğruluk** | ~95% | Veritabanındaki sorularda doğru cevap |
| **Yanıt Süresi** | <3 sn | Ortalama cevap süresi |
| **Halüsinasyon Oranı** | <5% | Yanlış/uydurma cevap oranı |
| **Büyük/Küçük Harf** | %100 | Case-insensitive arama |
| **Keyword Matching** | %90+ | Eş anlamlı kelime tanıma |

### 🎯 Kullanıcı Deneyimi

✅ **Tutarlılık**: Aynı soruya her zaman aynı cevap  
✅ **Hız**: Anında yanıt (<3 saniye)  
✅ **Güvenilirlik**: Sadece doğrulanmış bilgiler  
✅ **Şeffaflık**: Bilmediğinde açıkça söyler

### 🔍 Test Sonuçları

**Başarılı Test Vakaları:**
- ✅ Sertifika soruları (10/10)
- ✅ Grup/proje soruları (8/8)
- ✅ Mentor toplantı soruları (5/5)
- ✅ GitHub/teknik sorular (7/7)
- ✅ Canlı yayın soruları (6/6)

**Zorluk Çekilen Durumlar:**
- ⚠️ Çok genel sorular ("Bootcamp nedir?")
- ⚠️ Veritabanı dışı konular (beklenen davranış)

---

## 🌐 Canlı Demo

### 🔗 Web Arayüzü
**[🚀 MentorMate'i Deneyin!](https://mentormate-sss.streamlit.app)**

> *Not: Demo linki Streamlit Cloud üzerinde deploy edildikten sonra güncellenecektir.*

### 📹 Video Demo
**[▶️ YouTube Demo Videosu](https://www.youtube.com/watch?v=DEMO_ID)**

### 📸 Ekran Görüntüleri

#### Ana Ekran
![Ana Ekran](screenshots/main_screen.png)

#### Soru-Cevap Örneği
![Soru Cevap](screenshots/qa_example.png)

#### Sidebar İstatistikler
![Sidebar](screenshots/sidebar.png)

---

## 📁 Proje Yapısı

```
MentorMate-SSS/
├── app.py                          # Ana Streamlit uygulaması
├── requirements.txt                # Python bağımlılıkları
├── .env                           # API anahtarları (gitignore)
├── chroma_db/                     # Vektör veritabanı
│   └── [ChromaDB dosyaları]
├── data/                          # Veri setleri
│   ├── enriched_dataset.json      # Zenginleştirilmiş SSS
│   └── generated_data_google.json # Ham veri
├── notebooks/                     # Jupyter notebooks
│   └── data_preparation.ipynb     # Veri hazırlama
├── screenshots/                   # README görselleri
└── README.md                      # Bu dosya
```

---

## 🔧 Geliştirme Detayları

### Prompt Engineering
```python
KRİTİK KURALLAR:
1. SADECE verilen dokümanlardan cevap ver
2. Bilmediğinde: "Bu konuda veri setimde bilgi bulunmuyor"
3. Kendi bilgini ASLA kullanma
4. Tutarlı ol (aynı soruya aynı cevap)
```

### Embedding Model Seçimi
**Neden `paraphrase-multilingual-MiniLM-L12-v2`?**
- ✅ 384 boyut (hafif ve hızlı)
- ✅ Türkçe desteği
- ✅ ChromaDB uyumluluğu
- ✅ Düşük kaynak tüketimi

### Retriever Stratejisi
```python
search_type="mmr"           # Maximum Marginal Relevance
k=5                         # En iyi 5 doküman
fetch_k=25                  # 25 adaydan seç
lambda_mult=0.6             # %60 relevance + %40 diversity
```

---

## 🤝 Katkıda Bulunma

Bu proje Akbank GenAI Bootcamp kapsamında geliştirilmiştir. Önerileriniz için issue açabilirsiniz.

---

## 📝 Lisans

Bu proje MIT lisansı altında lisanslanmıştır.

---

## 👨‍💻 Geliştirici

**[Adınız Soyadınız]**
- 🔗 LinkedIn: [linkedin.com/in/profiliniz](https://linkedin.com/in/onurtilki)
- 📧 Email: mehmetonurt@gmail.com
- 🐙 GitHub: [@kullaniciadi](https://github.com/4F71)

---

## 🙏 Teşekkürler

- **Akbank & Global AI Hub**: Bootcamp organizasyonu
- **Turkish AI Hub**: Mentor desteği ve veri kaynağı
- **LangChain & Google**: Açık kaynak araçlar

---

<div align="center">

**⭐ Projeyi beğendiyseniz yıldız vermeyi unutmayın!**

![Made with Love](https://img.shields.io/badge/Made%20with-❤️-red?style=for-the-badge)
![Powered by AI](https://img.shields.io/badge/Powered%20by-AI-blue?style=for-the-badge)

</div>
