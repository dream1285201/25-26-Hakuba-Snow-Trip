# 25-26-Hakuba-Snow-Trip
白馬滑雪app

<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>❄️ 白馬雪國之旅 (移動版) ⛷️</title>
    <!-- 載入 Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 載入 Vue 3 (CDN 版本) -->
    <script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>
    <!-- 載入 Lucide Icons for aesthetic icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        /* CSS Vibe Coding: 日系極簡冰雪風 (Light Theme) */
        :root {
            --color-primary: #3b82f6; /* 冰雪藍 */
            --color-secondary: #f0f8ff; /* 淺雪白 */
            --color-text: #1e293b; /* 深藍灰 */
            --color-card: #ffffff; /* 卡片背景 */
            --color-onsen: #ff8f68; /* 溫泉橘 */
        }

        body {
            /* 主體背景改為淺藍色到白色的漸層 */
            background: linear-gradient(135deg, #f0f8ff, #e0f0ff, #ffffff); 
            font-family: 'Inter', sans-serif;
            color: var(--color-text); /* 文字改為深藍灰 */
            overflow-x: hidden; 
        }

        /* 模擬手機 App 容器，限制最大寬度，並置中 */
        #app {
            max-width: 450px;
            margin: 0 auto;
            min-height: 100vh;
            background-color: var(--color-card); /* App 容器底色為白色 */
            box-shadow: 0 0 40px rgba(0, 0, 0, 0.1); /* 柔和陰影 */
        }

        /* 行程卡片的過渡效果 */
        .details-enter-active, .details-leave-active {
            transition: all 0.3s ease-out;
            max-height: 300px;
        }
        .details-enter-from, .details-leave-to {
            opacity: 0;
            transform: translateY(-10px);
            max-height: 0;
            padding-top: 0 !important;
            padding-bottom: 0 !important;
        }
        
        /* 頁首圖片容器：高度固定 250px */
        .banner-container {
            height: 250px;
            overflow: hidden;
            background-color: #1a202c;
        }

        /* 溫泉標籤動畫 */
        .onsen-tag {
            animation: pulse-onsen 2s infinite;
        }

        @keyframes pulse-onsen {
            0%, 100% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.03); opacity: 0.8; }
        }

    </style>
</head>
<body>

<div id="app" class="pb-10">
    
    <!-- 1. 頁首 Banner (滿版冰雪山景) -->
    <div class="banner-container relative">
        <!-- 圖片容器: 檔案名稱: image_db5bdc.png (1920x400) -->
        <img src="https://placehold.co/1920x400/0f172a/94a3b8?text=Hakuba+Snow+Banner"
             onerror="this.onerror=null;this.src='https://placehold.co/1920x400/0f172a/94a3b8?text=Hakuba+Snow+Banner'"
             alt="白馬雪山橫幅"
             class="w-full h-full object-cover">
        
        <div class="absolute inset-0 bg-black bg-opacity-30 flex items-end p-6">
            <h1 class="text-3xl font-bold text-white tracking-wider drop-shadow-lg">
                白馬雪國之旅 🏔️
            </h1>
        </div>
    </div>

    <!-- 2. 行程列表 -->
    <main class="p-4 pt-6">
        <p class="text-sm text-gray-500 mb-6 text-center">11天10夜 / 12/27 - 01/06</p>

        <div v-for="(item, index) in itinerary" :key="index"
             class="itinerary-card bg-white rounded-xl mb-4 shadow-md overflow-hidden transition-all duration-300 hover:shadow-lg hover:shadow-sky-200/50 border border-gray-100">
            
            <!-- 卡片標題 (點擊區) -->
            <div @click="toggleDetails(index)"
                 class="p-4 flex justify-between items-center cursor-pointer transition-colors duration-200"
                 :class="{ 'bg-blue-50/70': item.expanded, 'bg-white': !item.expanded }">
                
                <div class="flex items-start flex-col">
                    <p class="text-xs text-blue-500 font-medium mb-1">{{ item.date }} | {{ item.day }} - 住宿: {{ item.accommodation }}</p>
                    <h2 class="text-lg font-semibold text-gray-800 flex items-center">
                        <i :data-lucide="getIconName(item.main)" class="w-5 h-5 mr-2" :class="getIconColor(item.main)"></i>
                        {{ item.main }}
                    </h2>
                    <span v-if="item.onsen" class="onsen-tag text-sm font-bold mt-1" :style="{ color: 'var(--color-onsen)' }">
                        ♨️ {{ item.onsen }}
                    </span>
                </div>
                
                <i :data-lucide="item.expanded ? 'chevron-up' : 'chevron-down'" class="w-6 h-6 text-blue-500 transition-transform duration-300"></i>
            </div>

            <!-- 3. 詳細內容 (可展開/收合) -->
            <Transition name="details">
                <div v-show="item.expanded" class="p-4 pt-2 border-t border-gray-200">
                    <h3 class="text-base font-semibold text-blue-600 mb-2">活動重點：{{ item.activity }}</h3>
                    <ul class="list-none p-0 space-y-2 text-sm text-gray-700">
                        <li v-for="(detail, i) in item.details" :key="i" class="flex items-start">
                            <span class="text-blue-500 mr-2 mt-1">•</span>
                            <span>{{ detail }}</span>
                        </li>
                    </ul>

                    <!-- 4. Google Maps & 導航整合 -->
                    <div class="mt-4 flex flex-col sm:flex-row space-y-2 sm:space-y-0 sm:space-x-2">
                        <a :href="getMapLink(item.mapLocation)" target="_blank"
                           class="flex-grow flex items-center justify-center p-2 rounded-lg text-white font-medium transition-colors duration-200 shadow-md"
                           :class="item.mapLocation ? 'bg-blue-500 hover:bg-blue-600' : 'bg-gray-400 cursor-not-allowed'"
                           :disabled="!item.mapLocation">
                            <i data-lucide="map-pin" class="w-4 h-4 mr-2"></i>
                            在地圖上查看 {{ item.mapLocationText || '地點' }}
                        </a>
                    </div>
                </div>
            </Transition>
        </div>
    </main>
</div>

<script>
    const { createApp, ref, onMounted } = Vue;

    createApp({
        setup() {
            // 定義 Day 7 和 Day 9 的內容，然後在 array 中進行對調
            const day7Content = {
                date: '01/02 (五)', day: 'Day 7', main: '滑雪日 5：五竜 & 47 ⛷️', activity: '地形與雪板公園', accommodation: '雪ノ音', onsen: '龍神之湯',
                mapLocation: '白馬五竜滑雪場', mapLocationText: '五竜滑雪場',
                details: [
                    '雪場特色：47有高品質的雪板公園，地形多變。',
                    '溫泉：龍神之湯 (Ryujin no Yu)，位於五竜 Escal Plaza 內，滑完直接泡湯最便利。'
                ], expanded: false
            };
            
            const day9Content = {
                date: '01/04 (日)', day: 'Day 9', main: '滑雪日 7：Cortina & Norikura ⛷️', activity: '粉雪追逐 (Powder Day)', accommodation: '雪ノ音', onsen: '',
                mapLocation: '白馬 Cortina 國際滑雪場', mapLocationText: 'Cortina',
                details: [
                    '雪場特色：素有「Powder Kingdom」美譽，若遇上新雪，務必前往。',
                    '注意：粉雪名氣大，若遇新雪人潮會較多。'
                ], expanded: false
            };

            const itinerary = ref([
                {
                    date: '12/27 (五)', day: 'Day 1', main: '抵達東京 (NRT) ✈️', activity: '長途駕駛至白馬', accommodation: '雪ノ音', onsen: '',
                    mapLocation: '諏訪湖サービスエリア', mapLocationText: '諏訪湖服務區',
                    details: [
                        '華航 CI104 12:35 出發。機場取車 (4WD + 雪胎)。',
                        '中途休息站：諏訪湖服務區 (Suwako SA)，確保輪流駕駛及充足休息。',
                        '預計深夜抵達白馬民宿，需提前通知民宿主人。'
                    ], expanded: false
                },
                {
                    date: '12/28 (日)', day: 'Day 2', main: '滑雪日 1：栂池高原 ⛷️', activity: '寬闊雪道暖身適應', accommodation: '雪ノ音', onsen: '',
                    mapLocation: '栂池高原滑雪場', mapLocationText: '栂池高原',
                    details: [
                        '雪場特色：白馬地區最寬闊緩坡，適合作為第一天的暖身。',
                        '建議：專注於基礎技巧，適應雪感。'
                    ], expanded: false
                },
                {
                    date: '12/29 (一)', day: 'Day 3', main: '滑雪日 2：白馬岩岳 ⛷️', activity: '中級雪道、山頂觀景', accommodation: '雪ノ音', onsen: '倉下之湯',
                    mapLocation: '倉下之湯', mapLocationText: '倉下之湯',
                    details: [
                        '雪場特色：必訪 HAKUBA MOUNTAIN HARBOR 觀景台。',
                        '溫泉：晚餐後前往倉下之湯，享受絕佳露天風呂與山景。'
                    ], expanded: false
                },
                {
                    date: '12/30 (二)', day: 'Day 4', main: '滑雪日 3：八方尾根 ⛷️', activity: '挑戰開始 - 奧運賽道', accommodation: '雪ノ音', onsen: '',
                    mapLocation: '八方尾根滑雪場', mapLocationText: '八方尾根',
                    details: [
                        '雪場特色：白馬最具挑戰性、垂直落差最大的雪場之一。',
                        '建議：從兎平 (Usagidaira) 開始適應。'
                    ], expanded: false
                },
                {
                    date: '12/31 (三)', day: 'Day 5', main: '滑雪日 4：八方尾根 ⛷️', activity: '指定日滑雪', accommodation: '雪ノ音', onsen: '八方溫泉 O-yu',
                    mapLocation: '八方溫泉 O-yu', mapLocationText: '八方溫泉 O-yu',
                    details: [
                        '策略：專注於前一天未完成的高級雪道。',
                        '溫泉：八方溫泉 O-yu，位於八方中心，強鹼性美肌之湯。',
                        '跨年：留意白馬村可能的跨年活動或煙火。'
                    ], expanded: false
                },
                {
                    date: '01/01 (四)', day: 'Day 6', main: '休息日：戶隱與小布施 ⛩️', activity: '新年參拜與文化美食', accommodation: '雪ノ音', onsen: '',
                    mapLocation: '信州小布施 北齋館', mapLocationText: '北齋館',
                    details: [
                        '上午：戶隱神社巨木林參拜 (1/1 人潮洶湧，務必早起)。',
                        '下午：前往小布施町，參觀北齋館，品嚐栗子甜點。',
                        '避峰：選擇小布施可避開善光寺元旦超級高峰人潮。'
                    ], expanded: false
                },
                // *** Day 7 內容改為 Cortina & Norikura ***
                day9Content,
                // ***************************************
                {
                    date: '01/03 (六)', day: 'Day 8', main: '滑雪日 6：Sanosaka ⛷️', activity: '青木湖景觀滑雪', accommodation: '雪ノ音', onsen: '',
                    mapLocation: '白馬 Sanosaka 滑雪場', mapLocationText: 'Sanosaka',
                    details: [
                        '雪場特色：人潮相對稀疏，擁有面向青木湖的絕美景觀。',
                        '建議：適合輕鬆滑行與拍照，享受寧靜滑雪日。'
                    ], expanded: false
                },
                // *** Day 9 內容改為 五竜 & 47 ***
                day7Content,
                // ***************************************
                {
                    date: '01/05 (一)', day: 'Day 10', main: '休息日：松本城 & 美食 ♨️', activity: '參觀國寶古城與溫泉', accommodation: '雪ノ音', onsen: '',
                    mapLocation: 'Soba-dokoro Kura', mapLocationText: '推薦餐廳: 蔵',
                    details: [
                        '上午/中午：自駕 1.5 小時到國寶級松本城參觀。',
                        '午餐：推薦蕎麥麵店 Soba-dokoro Kura (蔵)，體驗信州特色美食。',
                        '下午：返回白馬村，進行溫泉巡禮或最後採買。'
                    ], expanded: false
                },
                {
                    date: '01/06 (二)', day: 'Day 11', main: '返程：經輕井澤 🚗', activity: '開車回東京 (NRT)、還車、返台', accommodation: '行程結束', onsen: '',
                    mapLocation: '輕井澤', mapLocationText: '輕井澤',
                    details: [
                        '中途休息站：輕井澤 (Karuizawa)，可稍作休息與午餐。',
                        '注意：務必在 14:00 (下午兩點) 離開輕井澤，確保有充裕時間抵達 NRT 還車。',
                        '航班：華航 CI109 19:30 出發。'
                    ], expanded: false
                }
            ]);

            // 展開/收合卡片細節
            const toggleDetails = (index) => {
                itinerary.value[index].expanded = !itinerary.value[index].expanded;
                // 為了手機App的體驗，點擊時捲動到可視範圍
                if (itinerary.value[index].expanded) {
                    const cardElement = event.currentTarget.closest('.itinerary-card');
                    if (cardElement) {
                        setTimeout(() => {
                            // 延遲捲動，確保 Transition 完成
                            cardElement.scrollIntoView({ behavior: 'smooth', block: 'start' });
                        }, 300); 
                    }
                }
            };

            // 根據活動內容選擇 Lucide Icon
            const getIconName = (main) => {
                if (main.includes('滑雪')) return 'snowflake';
                if (main.includes('抵達') || main.includes('返程')) return 'plane-takeoff';
                if (main.includes('神社') || main.includes('松本城') || main.includes('小布施')) return 'temple';
                if (main.includes('溫泉') || main.includes('休息')) return 'heart-handshake';
                return 'map-pin';
            };

            // 根據活動內容選擇圖標顏色
            const getIconColor = (main) => {
                if (main.includes('滑雪')) return 'text-blue-500';
                if (main.includes('抵達') || main.includes('返程')) return 'text-indigo-600';
                if (main.includes('神社') || main.includes('松本城') || main.includes('小布施')) return 'text-amber-500';
                if (main.includes('溫泉') || main.includes('休息')) return 'text-red-500';
                return 'text-gray-500';
            };
            
            // 生成 Google Maps 連結 (使用 https://maps.google.com/search?q= 格式)
            const getMapLink = (location) => {
                if (!location) return '#';
                // 使用 Google Maps 搜尋連結，自動開啟導航/地圖應用
                const base = 'https://www.google.com/maps/search/';
                const encodedLocation = encodeURIComponent(location);
                return `${base}${encodedLocation}`;
            };
            
            // Vue 組件掛載後初始化 Lucide Icons
            onMounted(() => {
                lucide.createIcons();
            });

            return {
                itinerary,
                toggleDetails,
                getIconName,
                getIconColor,
                getMapLink
            };
        }
    }).mount('#app');
</script>

</body>
</html>
