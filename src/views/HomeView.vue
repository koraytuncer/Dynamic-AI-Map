<template>
  <div class="relative h-[100vh] w-full overflow-hidden bg-slate-900">
    <!-- Map Container with overlay -->
    <div ref="mapContainer" class="h-full w-full z-10 touch-pan-y overflow-hidden"></div>
    
    <!-- Dark Overlay for better contrast -->
    <div class="absolute inset-0 bg-black/10 pointer-events-none z-20"></div>

    <!-- Search Box -->
    <div class="absolute top-6 left-1/2 transform -translate-x-1/2 z-30 w-full max-w-2xl px-4">
      <div class="relative">
        <input
          v-model="searchQuery"
          @keyup.enter="handleSearch"
          type="text"
          placeholder="Şehir ara veya soru sor... (örn: 'Amasya' veya 'Elması ile meşhur şehir')"
          class="w-full px-6 py-4 pl-14 rounded-xl shadow-lg 
                 bg-white/10 backdrop-blur-md border border-white/20
                 text-dark placeholder-gray-400
                 focus:border-blue-500 focus:outline-none focus:ring-2 focus:ring-blue-500/40
                 transition-all duration-300"
        />
        <span class="absolute left-5 top-1/2 -translate-y-1/2 text-gray-400">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
          </svg>
        </span>
        <button
          @click="handleSearch"
          class="absolute right-4 top-1/2 -translate-y-1/2 
                 bg-slate-900/95 hover:bg-slate-800/95 
                 text-white px-6 py-2 rounded-lg
                 backdrop-blur-sm transition-all duration-300
                 border border-white/10
                 shadow-lg shadow-black/10
                 focus:outline-none focus:ring-2 focus:ring-slate-500/40
                 group"
        >
          <span class="flex items-center space-x-2">
            <span>Ara</span>
           
          </span>
        </button>
      </div>
    </div>

    <!-- Info Drawer - Desktop -->
    <div
      v-if="showInfo"
      class="fixed left-0 top-0 h-full w-[420px] z-40
             bg-slate-900/95 backdrop-blur-xl
             shadow-2xl shadow-black/50
             transform transition-transform duration-500 ease-out
             border-r border-white/10
             hidden lg:block"
      :class="{ '-translate-x-full': !showInfo }"
    >
      <div class="h-full flex flex-col">
        <!-- Header -->
        <div class="p-6 border-b border-white/10">
          <div class="flex justify-between items-start">
            <div>
              <h3 class="text-2xl font-bold text-white">{{ currentLocation }}</h3>
              <div class="flex items-center mt-2 space-x-2">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                </svg>
                <span class="text-gray-400">Türkiye</span>
              </div>
            </div>
            <button
              @click="showInfo = false"
              class="text-gray-400 hover:text-white p-2 hover:bg-white/5 rounded-full transition-colors"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>
        </div>

        <!-- Drawer Content -->
        <div class="flex-1 overflow-y-auto custom-scrollbar">
          <!-- City Image Section -->
          <div class="p-6 pb-0">
            <div class="aspect-video rounded-xl overflow-hidden bg-slate-800">
              <img
                :src="cityImage"
                alt="Şehir Görünümü"
                class="w-full h-full object-cover"
                @error="handleImageError"
              />
            </div>
          </div>

          <!-- Quick Info Section -->
          <div class="grid grid-cols-2 gap-4 p-6">
            <div class="bg-white/5 p-4 rounded-xl backdrop-blur-sm border border-white/10">
              <span class="text-sm text-gray-400">{{ plateCode === "Konum" ? "Konum" : "Nüfus" }}</span>
              <p class="text-lg font-semibold text-white mt-1">{{ population }}</p>
            </div>
            <div class="bg-white/5 p-4 rounded-xl backdrop-blur-sm border border-white/10">
              <span class="text-sm text-gray-400">{{ plateCode === "Konum" ? "Tür" : "Plaka" }}</span>
              <p class="text-lg font-semibold text-white mt-1">{{ plateCode }}</p>
            </div>
          </div>

          <!-- Description Section -->
          <div class="px-6 pb-6">
            <h4 class="text-sm font-medium text-gray-400 uppercase tracking-wide mb-4">
              Şehir Hakkında
            </h4>
            <div class="prose prose-sm prose-invert max-w-none">
              <p class="text-gray-300 leading-relaxed">{{ locationInfo }}</p>
            </div>
          </div>

          <!-- Additional Info -->
          <div class="px-6 pb-6">
            <div class="rounded-xl bg-blue-500/10 border border-blue-500/20 p-4">
              <div class="flex">
                <div class="flex-shrink-0">
                  <svg class="h-5 w-5 text-blue-400" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z" clip-rule="evenodd" />
                  </svg>
                </div>
                <div class="ml-3">
                  <h3 class="text-sm font-medium text-blue-400">Bilgi</h3>
                  <div class="mt-2 text-sm text-blue-300">
                    <p>Haritada işaretli konumu görmek için yakınlaştırabilirsiniz.</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Info Sheet - Mobile & Tablet -->
    <div
      v-if="showInfo"
      class="fixed inset-0 bg-black bg-opacity-25 z-[1000] lg:hidden"
      @click.self="showInfo = false"
    >
      <div
        class="absolute inset-x-0 bottom-0 bg-white rounded-t-2xl shadow-xl transform transition-transform duration-300 ease-in-out"
        :class="{ 'translate-y-full': !showInfo }"
        style="height: 85vh; max-height: 85vh"
      >
        <!-- Handle bar -->
        <div class="w-12 h-1.5 bg-gray-300 rounded-full mx-auto my-3"></div>

        <!-- Sheet Header -->
        <div class="px-6 mb-6">
          <div class="flex justify-between items-start">
            <div>
              <h3 class="text-2xl font-bold text-gray-900">
                {{ currentLocation }}
              </h3>
              <div class="flex items-center mt-2 space-x-2">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-4 w-4 text-gray-400"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z"
                  />
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M15 11a3 3 0 11-6 0 3 3 0 016 0z"
                  />
                </svg>
                <span class="text-sm text-gray-500">Türkiye</span>
              </div>
            </div>
            <button
              @click="showInfo = false"
              class="text-gray-400 hover:text-gray-600 transition-colors p-1 hover:bg-gray-100 rounded-full"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-5 w-5"
                fill="none"
                viewBox="0 0 24 24"
                stroke="currentColor"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </button>
          </div>
        </div>

        <!-- Sheet Content -->
        <div
          class="px-6 pb-6 overflow-y-auto"
          style="height: calc(85vh - 48px - 1.5rem)"
        >
          <!-- City Image Section -->
          <div class="mb-6">
            <div class="aspect-video rounded-lg overflow-hidden bg-gray-100">
              <img
                :src="cityImage"
                alt="Şehir Görünümü"
                class="w-full h-full object-cover"
                @error="handleImageError"
              />
            </div>
          </div>

          <!-- Quick Info Section -->
          <div class="grid grid-cols-2 gap-4 mb-6">
            <div class="bg-gray-50 p-4 rounded-lg">
              <span class="text-sm text-gray-500">{{
                plateCode === "Konum" ? "Konum" : "Nüfus"
              }}</span>
              <p class="text-lg font-semibold text-gray-900 mt-1">
                {{ population }}
              </p>
            </div>
            <div class="bg-gray-50 p-4 rounded-lg">
              <span class="text-sm text-gray-500">{{
                plateCode === "Konum" ? "Tür" : "Plaka"
              }}</span>
              <p class="text-lg font-semibold text-gray-900 mt-1">
                {{ plateCode }}
              </p>
            </div>
          </div>

          <!-- Description Section -->
          <div class="mb-6">
            <h4
              class="text-sm font-medium text-gray-500 uppercase tracking-wide mb-4"
            >
              Şehir Hakkında
            </h4>
            <div class="prose prose-sm max-w-none">
              <p class="text-gray-600 leading-relaxed">{{ locationInfo }}</p>
            </div>
          </div>

          <!-- Additional Info -->
          <div class="rounded-lg bg-blue-50 p-4">
            <div class="flex">
              <div class="flex-shrink-0">
                <svg
                  class="h-5 w-5 text-blue-400"
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 20 20"
                  fill="currentColor"
                >
                  <path
                    fill-rule="evenodd"
                    d="M18 10a8 8 0 11-16 0 8 8 0 0116 0zm-7-4a1 1 0 11-2 0 1 1 0 012 0zM9 9a1 1 0 000 2v3a1 1 0 001 1h1a1 1 0 100-2v-3a1 1 0 00-1-1H9z"
                    clip-rule="evenodd"
                  />
                </svg>
              </div>
              <div class="ml-3">
                <h3 class="text-sm font-medium text-blue-800">Bilgi</h3>
                <div class="mt-2 text-sm text-blue-700">
                  <p>
                    Haritada işaretli konumu görmek için yakınlaştırabilirsiniz.
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Loading Indicator -->
    <div
      v-if="isLoading"
      class="absolute inset-0 bg-black bg-opacity-50 flex items-center justify-center z-[2000]"
    >
      <div class="bg-white p-4 rounded-lg shadow-lg">
        <div
          class="animate-spin rounded-full h-8 w-8 border-b-2 border-blue-500 mx-auto"
        ></div>
        <p class="mt-2 text-gray-600">Aranıyor...</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick } from "vue";
import L from "leaflet";
import axios from "axios";
import { useGeolocation } from "@vueuse/core";

// State variables using refs
const mapContainer = ref(null);
const map = ref(null);
const marker = ref(null);
const searchQuery = ref("");
const showInfo = ref(false);
const currentLocation = ref("");
const locationInfo = ref("");
const population = ref("");
const plateCode = ref("");
const cityImage = ref("");
const isLoading = ref(false);

// Geolocation hook
const { coords } = useGeolocation();

// Map initialization
const initializeMap = () => {
  // Fix Leaflet icon issue
  delete L.Icon.Default.prototype._getIconUrl;
  L.Icon.Default.mergeOptions({
    iconRetinaUrl: new URL("leaflet/dist/images/marker-icon-2x.png", import.meta.url).href,
    iconUrl: new URL("leaflet/dist/images/marker-icon.png", import.meta.url).href,
    shadowUrl: new URL("leaflet/dist/images/marker-shadow.png", import.meta.url).href,
  });

  // Create map instance
  map.value = L.map(mapContainer.value).setView([39.9334, 32.8597], 6);

  // Add tile layer
  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
    attribution: "© OpenStreetMap contributors",
  }).addTo(map.value);

  // Create initial marker (hidden)
  marker.value = L.marker([0, 0], { draggable: false });
};

// Lifecycle hooks
onMounted(() => {
  initializeMap()
  
  // Harita yüklendikten sonra invalidateSize çağıralım
  nextTick(() => {
    map.value?.invalidateSize()
  })

  // Ekran döndürme ve resize olaylarını dinleyelim
  window.addEventListener('resize', () => {
    map.value?.invalidateSize()
  })

  window.addEventListener('orientationchange', () => {
    setTimeout(() => {
      map.value?.invalidateSize()
    }, 200)
  })
})
onUnmounted(() => map.value?.remove());

// API Handlers
const getGeminiResponse = async (query) => {
  const GEMINI_API_KEY = import.meta.env.VITE_GOOGLE_MAPS_API_KEY;

  const response = await axios.post(
    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent?key=${GEMINI_API_KEY}`,
    {
      contents: [{
        parts: [{
          text: `Türkiye ile ilgili verilen soruyu yanıtla. Eğer belirli bir şehir soruluyorsa veya cevap belirli bir şehri işaret ediyorsa, o şehrin bilgilerini de ekle.

Örnek soru tipleri:
1. "Kayısısı ile meşhur şehrimiz" -> Malatya hakkında bilgi ver
2. "Atatürk'ün kabri nerede?" -> Anıtkabir'in Ankara'da olduğunu belirt ve bilgi ver
3. "İstanbul'da vapurların en yoğun olduğu yer" -> Eminönü/Kadıköy iskelesi hakkında bilgi ver

Yanıtı şu JSON formatında ver:
{
  "type": "city|location|info",
  "title": "Başlık (şehir adı veya yer adı)",
  "coordinates": [enlem, boylam],
  "description": "Kısa açıklama (maksimum 200 karakter)",
  "population": "Nüfus (sadece şehir sorularında)",
  "plateCode": "Plaka kodu (sadece şehir sorularında)",
  "locationInfo": "Konum bilgisi (örn: Ankara, Çankaya)"
}

Soru: "${query}"`,
        }]
      }]
    },
    {
      headers: { "Content-Type": "application/json" }
    }
  );

  const result = response.data.candidates[0].content.parts[0].text;
  console.log("Gemini API Response:", result);
  return result;
};

const getCityImage = async (cityName) => {
  try {
    // Try with city+country first
    const getWikiImage = async (title) => {
      const response = await axios.get(
        `https://tr.wikipedia.org/w/api.php?action=query&titles=${title}&prop=pageimages&format=json&pithumbsize=800&origin=*`
      );
      return response.data.query.pages;
    };

    let pages = await getWikiImage(`${cityName},_Türkiye`);
    let pageId = Object.keys(pages)[0];

    // If no result, try with city name only
    if (!pages[pageId].thumbnail) {
      pages = await getWikiImage(cityName);
      pageId = Object.keys(pages)[0];
    }

    return pages[pageId].thumbnail?.source || "/default-city.jpg";
  } catch (error) {
    console.error("Wikipedia image error:", error);
    return "/default-city.jpg";
  }
};

// Event Handlers
const handleSearch = async () => {
  if (!searchQuery.value) return;

  isLoading.value = true;
  try {
    const aiResponse = await getGeminiResponse(searchQuery.value);
    const parsedResponse = parseAIResponse(aiResponse);

    if (parsedResponse.coordinates) {
      // Update map and marker
      map.value.setView(parsedResponse.coordinates, 14);
      marker.value.setLatLng(parsedResponse.coordinates).addTo(map.value);

      // Get and set image
      const imageUrl = await getCityImage(parsedResponse.title);

      // Update state
      Object.assign(currentLocation, {
        value: parsedResponse.title
      });
      
      locationInfo.value = parsedResponse.description;

      // Set population and plate code based on response type
      if (parsedResponse.type === "city") {
        population.value = parsedResponse.population;
        plateCode.value = parsedResponse.plateCode;
      } else {
        population.value = parsedResponse.locationInfo;
        plateCode.value = parsedResponse.type === "location" ? "Konum" : "Bilgi";
      }

      cityImage.value = imageUrl;
      showInfo.value = true;
    }
  } catch (error) {
    console.error("Search error:", error);
    alert("Arama sırasında bir hata oluştu. Lütfen tekrar deneyin.");
  } finally {
    isLoading.value = false;
  }
};

const handleImageError = (e) => {
  e.target.src = "assets/default-city.jpg";
};

// Utility Functions
const parseAIResponse = (response) => {
  try {
    const jsonMatch = response.match(/\{[\s\S]*\}/);
    const jsonStr = jsonMatch ? jsonMatch[0] : response;
    return JSON.parse(jsonStr);
  } catch (error) {
    console.error("Parse error:", error);
    throw new Error("AI yanıtı işlenemedi");
  }
};
</script>

<style>
@import "leaflet/dist/leaflet.css";

/* Drawer ve Sheet animasyonları için ek stiller */
.translate-y-full {
  transform: translateY(100%);
}

.-translate-x-full {
  transform: translateX(-100%);
}

/* Mobil cihazlarda kaydırma davranışını iyileştirme */
.overflow-y-auto {
  -webkit-overflow-scrolling: touch;
  scrollbar-width: thin;
  scrollbar-color: rgba(156, 163, 175, 0.5) transparent;
}

.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: transparent;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background-color: rgba(156, 163, 175, 0.5);
  border-radius: 3px;
}

/* Prose stilleri */
.prose {
  max-width: 65ch;
}

.prose p {
  margin-top: 1.25em;
  margin-bottom: 1.25em;
}

/* iOS için -webkit-fill-available kullanımı */
.h-\[100vh\] {
  height: 100vh;
  height: -webkit-fill-available;
}

/* iOS'ta bounce scroll'u engelleyelim */
html, body {
  position: fixed;
  width: 100%;
  height: 100%;
  overflow: hidden;
  -webkit-overflow-scrolling: touch;
}

/* Leaflet container için ek stiller */
.leaflet-container {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
</style>
