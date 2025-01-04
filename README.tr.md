# 🗺️ Dinamik Türkiye Bilgi Haritası

Yapay zeka destekli interaktif Türkiye haritası ve bilgi sistemi. Google'ın Gemini AI teknolojisini kullanarak, Türkiye'nin şehirleri ve önemli lokasyonları hakkında dinamik bilgiler sunar.



[🇬🇧 English README](README.md)


[Demo](https://dynamic-ai-map.vercel.app/)'a gidin


## 🚀 Özellikler

- 🤖 Gemini AI ile doğal dil sorguları
- 🗺️ İnteraktif harita görünümü
- 📱 Tam responsive tasarım
- 🎨 Modern ve şık arayüz
- 🔍 Akıllı arama sistemi
- 📍 Otomatik lokasyon tespiti
- 🖼️ Wikipedia entegrasyonu ile görsel içerik

## 🛠️ Teknolojiler

- Vue.js 3 - Frontend framework
- Vite - Build tool
- Tailwind CSS - Stil kütüphanesi
- Leaflet.js - Harita kütüphanesi
- OpenStreetMap - Harita veri sağlayıcı
- Google Gemini AI - Yapay zeka API'si
- Wikipedia API - Görsel içerik

## ⚙️ Kurulum

1. Repoyu klonlayın:

2. Bağımlılıkları yükleyin:

3. `.env.example` dosyasını `.env` olarak kopyalayın:

4. `.env` dosyasını düzenleyin:
- `VITE_GOOGLE_MAPS_API_KEY`: Google Gemini API anahtarınızı ekleyin
- Diğer yapılandırma değerlerini isteğe bağlı olarak özelleştirin

5. Geliştirme sunucusunu başlatın:

## 🔑 API Anahtarı Alma

1. [Google Cloud Console](https://console.cloud.google.com/)'a gidin
2. Yeni bir proje oluşturun
3. Gemini API'yi etkinleştirin
4. API anahtarı oluşturun
5. Oluşturulan anahtarı `.env` dosyasına ekleyin

## 🗺️ Harita Sistemi

Proje iki temel harita bileşeni kullanır:
- **Leaflet.js**: Harita görüntüleme ve etkileşim kütüphanesi
- **OpenStreetMap**: Açık kaynak harita verileri sağlayıcısı

Bu kombinasyon sayesinde:
- Yüksek performanslı harita görüntüleme
- Zengin harita etkileşim özellikleri
- Güvenilir ve güncel harita verileri
- Ücretsiz ve açık kaynak altyapı

## 📝 Kullanım

1. Arama kutusuna sorgunuzu yazın:
   - Direkt şehir adı (örn: "İstanbul")
   - Özellik sorgusu (örn: "Kayısısı ile meşhur şehir")
   - Lokasyon sorgusu (örn: "Atatürk'ün kabri nerede?")

2. Enter'a basın veya Ara butonuna tıklayın
3. Sonuçlar harita üzerinde gösterilecek ve detaylı bilgiler yan panelde açılacaktır

## 🚀 Production Build

Production build almak için:

npm run build

Build dosyaları `dist` klasöründe oluşturulacaktır.

## 🤝 Katkıda Bulunma

1. Bu repoyu fork edin
2. Feature branch oluşturun (`git checkout -b feature/YeniOzellik`)
3. Değişikliklerinizi commit edin (`git commit -m 'Yeni özellik eklendi'`)
4. Branch'inizi push edin (`git push origin feature/YeniOzellik`)
5. Pull Request oluşturun

## 📄 Lisans

Bu proje MIT lisansı altında lisanslanmıştır. Detaylar için [LICENSE](LICENSE) dosyasına bakın.

## 👏 Teşekkürler

- [Google Gemini AI](https://deepmind.google/technologies/gemini/)
- [OpenStreetMap](https://www.openstreetmap.org/)
- [Wikipedia](https://www.wikipedia.org/)

## 📞 İletişim

Proje Sahibi - [Koray Tuncer](https://www.linkedin.com/in/koraytuncer/)

Proje Linki: [https://github.com/koraytuncer/dynamic-turkey-map](https://github.com/koraytuncer/dynamic-turkey-map)