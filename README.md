<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ফরিদপুর ইফতার - গুগল ম্যাপ সহ</title>
    <!-- ফন্ট ও স্টাইল -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <!-- ফন্ট অসম সিডিএন -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <!-- লিফলেট ম্যাপ সিডিএন (গুগল ম্যাপের মত ফ্রি) -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Hind Siliguri', sans-serif;
            background: #f0f2f5;
            color: #2c3e50;
        }
        
        /* হেডার */
        .header {
            background: linear-gradient(135deg, #1a5f4b 0%, #0d3d2f 100%);
            color: white;
            padding: 30px 20px;
            text-align: center;
            box-shadow: 0 4px 20px rgba(0,0,0,0.2);
        }
        
        .header h1 {
            font-size: 2.8rem;
            margin-bottom: 10px;
        }
        
        .header h1 i {
            color: #ffd700;
            margin-right: 10px;
        }
        
        .header p {
            font-size: 1.2rem;
            opacity: 0.9;
        }
        
        /* এলাকা সিলেক্টর */
        .area-selector {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin: 30px auto;
            max-width: 1200px;
            padding: 0 20px;
            flex-wrap: wrap;
        }
        
        .area-btn {
            background: white;
            border: none;
            padding: 15px 35px;
            border-radius: 50px;
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            transition: all 0.3s;
            font-family: 'Hind Siliguri', sans-serif;
            border: 2px solid transparent;
        }
        
        .area-btn:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 25px rgba(0,0,0,0.15);
        }
        
        .area-btn.active {
            background: #1a5f4b;
            color: white;
            border-color: #ffd700;
        }
        
        .area-btn i {
            margin-right: 10px;
            color: #ffd700;
        }
        
        /* মূল লেআউট - গুগল ম্যাপের মত */
        .main-layout {
            display: grid;
            grid-template-columns: 400px 1fr;
            gap: 20px;
            max-width: 1400px;
            margin: 20px auto;
            padding: 0 20px;
            height: calc(100vh - 250px);
            min-height: 600px;
        }
        
        /* সাইডবার - মসজিদের তালিকা */
        .sidebar {
            background: white;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            overflow: hidden;
            display: flex;
            flex-direction: column;
        }
        
        .sidebar-header {
            padding: 20px;
            background: #f8f9fa;
            border-bottom: 2px solid #e9ecef;
        }
        
        .sidebar-header h3 {
            font-size: 1.5rem;
            color: #1a5f4b;
            margin-bottom: 10px;
        }
        
        .search-box {
            display: flex;
            gap: 10px;
        }
        
        .search-box input {
            flex: 1;
            padding: 12px 15px;
            border: 2px solid #dee2e6;
            border-radius: 12px;
            font-family: 'Hind Siliguri', sans-serif;
            font-size: 1rem;
        }
        
        .search-box button {
            background: #1a5f4b;
            color: white;
            border: none;
            width: 50px;
            border-radius: 12px;
            cursor: pointer;
            transition: background 0.3s;
        }
        
        .search-box button:hover {
            background: #0d3d2f;
        }
        
        .mosque-list {
            flex: 1;
            overflow-y: auto;
            padding: 15px;
        }
        
        .list-item {
            display: flex;
            gap: 15px;
            padding: 15px;
            border-bottom: 1px solid #e9ecef;
            cursor: pointer;
            transition: all 0.3s;
            border-radius: 12px;
        }
        
        .list-item:hover {
            background: #f8f9fa;
            transform: translateX(5px);
        }
        
        .list-item.active {
            background: #e8f4f0;
            border-left: 4px solid #1a5f4b;
        }
        
        .list-icon {
            width: 50px;
            height: 50px;
            background: #1a5f4b;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #ffd700;
            font-size: 1.5rem;
        }
        
        .list-info h4 {
            font-size: 1.1rem;
            margin-bottom: 5px;
            color: #2c3e50;
        }
        
        .list-info p {
            font-size: 0.9rem;
            color: #6c757d;
            margin-bottom: 3px;
        }
        
        .list-info .badge {
            display: inline-block;
            padding: 3px 10px;
            background: #ffd700;
            color: #2c3e50;
            border-radius: 15px;
            font-size: 0.8rem;
            font-weight: 600;
            margin-right: 5px;
        }
        
        /* ম্যাপ কন্টেইনার */
        .map-container {
            background: #e9ecef;
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            position: relative;
        }
        
        #map {
            height: 100%;
            width: 100%;
            z-index: 1;
        }
        
        /* ম্যাপ কন্ট্রোল */
        .map-controls {
            position: absolute;
            top: 20px;
            right: 20px;
            z-index: 1000;
            background: white;
            border-radius: 12px;
            padding: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }
        
        .map-controls button {
            display: block;
            width: 40px;
            height: 40px;
            margin: 5px 0;
            border: none;
            background: white;
            border-radius: 8px;
            cursor: pointer;
            font-size: 1.2rem;
            color: #1a5f4b;
            transition: all 0.3s;
        }
        
        .map-controls button:hover {
            background: #1a5f4b;
            color: white;
        }
        
        /* ইনফো উইন্ডো স্টাইল */
        .custom-popup .leaflet-popup-content-wrapper {
            background: white;
            border-radius: 15px;
            padding: 0;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }
        
        .custom-popup .leaflet-popup-content {
            margin: 0;
            min-width: 280px;
        }
        
        .popup-content {
            padding: 15px;
        }
        
        .popup-content h3 {
            color: #1a5f4b;
            margin-bottom: 10px;
            font-size: 1.3rem;
            border-bottom: 2px solid #ffd700;
            padding-bottom: 8px;
        }
        
        .popup-info {
            margin: 12px 0;
        }
        
        .popup-info p {
            margin: 8px 0;
            display: flex;
            align-items: center;
            gap: 10px;
            color: #495057;
        }
        
        .popup-info i {
            width: 20px;
            color: #1a5f4b;
        }
        
        .popup-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin: 15px 0;
        }
        
        .popup-tag {
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.9rem;
            font-weight: 500;
        }
        
        .popup-tag.biriyani {
            background: #e3f2e9;
            color: #1a5f4b;
        }
        
        .popup-tag.iftar {
            background: #fff3e0;
            color: #e67e22;
        }
        
        .popup-footer {
            display: flex;
            gap: 10px;
            margin-top: 15px;
        }
        
        .popup-btn {
            flex: 1;
            padding: 10px;
            text-align: center;
            border-radius: 10px;
            text-decoration: none;
            font-weight: 500;
            transition: all 0.3s;
        }
        
        .popup-btn.direction {
            background: #1a5f4b;
            color: white;
        }
        
        .popup-btn.direction:hover {
            background: #0d3d2f;
        }
        
        .popup-btn.call {
            background: #ffd700;
            color: #2c3e50;
        }
        
        .popup-btn.call:hover {
            background: #e6c200;
        }
        
        /* ফুটার */
        .footer {
            background: #2c3e50;
            color: white;
            text-align: center;
            padding: 30px 20px;
            margin-top: 40px;
        }
        
        /* রেসপন্সিভ */
        @media (max-width: 968px) {
            .main-layout {
                grid-template-columns: 1fr;
                height: auto;
            }
            
            .sidebar {
                height: 400px;
            }
            
            .map-container {
                height: 500px;
            }
        }
    </style>
</head>
<body>
    <!-- হেডার -->
    <div class="header">
        <h1>
            <i class="fas fa-mosque"></i>
            ফরিদপুর ইফতার ম্যাপ
        </h1>
        <p>ফরিদপুর সদর, পাকুড়িয়া ও আলফাডাঙ্গার মসজিদের ইন্টারেক্টিভ ম্যাপ</p>
    </div>
    
    <!-- এলাকা সিলেক্টর -->
    <div class="area-selector">
        <button class="area-btn active" onclick="changeArea('faridpur')">
            <i class="fas fa-city"></i> ফরিদপুর সদর
        </button>
        <button class="area-btn" onclick="changeArea('pakuria')">
            <i class="fas fa-location-dot"></i> পাকুড়িয়া
        </button>
        <button class="area-btn" onclick="changeArea('alfadanga')">
            <i class="fas fa-map-marked-alt"></i> আলফাডাঙ্গা
        </button>
    </div>
    
    <!-- মূল লেআউট - গুগল ম্যাপ স্টাইল -->
    <div class="main-layout">
        <!-- সাইডবার - মসজিদের তালিকা -->
        <div class="sidebar">
            <div class="sidebar-header">
                <h3><i class="fas fa-list"></i> মসজিদের তালিকা</h3>
                <div class="search-box">
                    <input type="text" id="searchInput" placeholder="মসজিদ খুঁজুন...">
                    <button onclick="searchMosques()"><i class="fas fa-search"></i></button>
                </div>
            </div>
            <div class="mosque-list" id="mosqueList"></div>
        </div>
        
        <!-- ম্যাপ কন্টেইনার -->
        <div class="map-container">
            <div id="map"></div>
            <div class="map-controls">
                <button onclick="map.zoomIn()"><i class="fas fa-plus"></i></button>
                <button onclick="map.zoomOut()"><i class="fas fa-minus"></i></button>
                <button onclick="locateUser()"><i class="fas fa-location-dot"></i></button>
            </div>
        </div>
    </div>
    
    <!-- ফুটার -->
    <div class="footer">
        <p><i class="fas fa-heart" style="color: #ffd700;"></i> ফরিদপুরবাসীর জন্য ভালোবাসায় তৈরি</p>
    </div>

    <script>
        // মসজিদের ডাটা
        const mosques = [
            // ফরিদপুর সদর
            {
                id: 1,
                name: "ফরিদপুর কেন্দ্রীয় জামে মসজিদ",
                area: "ফরিদপুর সদর",
                address: "কালেক্টরেট এলাকা, ফরিদপুর সদর",
                lat: 23.5961,
                lng: 89.8425,
                phone: "০১৭১২-৩৩৪৪৫৫",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["মুরগি বিরিয়ানি", "খাসি বিরিয়ানি (শুক্রবার)"],
                iftar: ["খেজুর", "জিলাপি", "ছোলা"],
                hasBiriyani: true
            },
            {
                id: 2,
                name: "কোতোয়ালী জামে মসজিদ",
                area: "ফরিদপুর সদর",
                address: "কোতোয়ালী থানা রোড, ফরিদপুর",
                lat: 23.5985,
                lng: 89.8440,
                phone: "০১৭৪৫-৬৭৮৯০১",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["স্পেশাল মুরগি বিরিয়ানি"],
                iftar: ["শরবত", "ছোলা", "মুড়ি"],
                hasBiriyani: true
            },
            {
                id: 3,
                name: "বড় মসজিদ (স্টেডিয়াম সংলগ্ন)",
                area: "ফরিদপুর সদর",
                address: "স্টেডিয়াম রোড, ফরিদপুর",
                lat: 23.6012,
                lng: 89.8468,
                phone: "০১৩২২-৫৫৪৪৩৩",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["গরুর মাংসের বিরিয়ানি"],
                iftar: ["মুড়ি", "হালিম"],
                hasBiriyani: true
            },
            {
                id: 4,
                name: "রাজেন্দ্রপুর জামে মসজিদ",
                area: "ফরিদপুর সদর",
                address: "রাজেন্দ্রপুর, ফরিদপুর সদর",
                lat: 23.5915,
                lng: 89.8510,
                phone: "০১৯৯৯-১১২২৩৩",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["খাসি বিরিয়ানি"],
                iftar: ["পিঁয়াজু", "বেগুনি"],
                hasBiriyani: true
            },
            {
                id: 5,
                name: "শাহী জামে মসজিদ",
                area: "ফরিদপুর সদর",
                address: "শাহী মসজিদ রোড, ফরিদপুর",
                lat: 23.5940,
                lng: 89.8485,
                phone: "০১৭৮৮-৯৯২২৩৩",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["খাসির বিরিয়ানি"],
                iftar: ["জিলাপি", "ছোলা"],
                hasBiriyani: true
            },
            {
                id: 6,
                name: "মদিনা জামে মসজিদ",
                area: "ফরিদপুর সদর",
                address: "আমবাগান, ফরিদপুর",
                lat: 23.6025,
                lng: 89.8410,
                phone: "০১৭৫৫-৬৬৭৭৮৮",
                iftarTime: "সন্ধ্যা ৬:২৩",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["হালিম", "বেগুনি"],
                hasBiriyani: true
            },
            
            // আলফাডাঙ্গা
            {
                id: 7,
                name: "আলফাডাঙ্গা কেন্দ্রিয় জামে মসজিদ",
                area: "আলফাডাঙ্গা",
                address: "আলফাডাঙ্গা সদর, ফরিদপুর",
                lat: 23.2867,
                lng: 89.7133,
                phone: "০১৭১২-৩৩৪৪৫৫",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["খেজুর", "জিলাপি"],
                hasBiriyani: true
            },
            {
                id: 8,
                name: "আলফাডাঙ্গা হাইস্কুল জামে মসজিদ",
                area: "আলফাডাঙ্গা",
                address: "আলফাডাঙ্গা হাইস্কুল, ফরিদপুর",
                lat: 23.2855,
                lng: 89.7148,
                phone: "০১৭৪৫-৬৭৮৯০১",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["ছোলা", "মুড়ি"],
                hasBiriyani: true
            },
            {
                id: 9,
                name: "কুসুমদি জামে মসজিদ",
                area: "আলফাডাঙ্গা",
                address: "কুসুমদি, আলফাডাঙ্গা",
                lat: 23.2750,
                lng: 89.7050,
                phone: "০১৯৯৯-১১২২৩৩",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["হালিম"],
                hasBiriyani: true
            },
            {
                id: 10,
                name: "ইছাপাশা জামে মসজিদ",
                area: "আলফাডাঙ্গা",
                address: "ইছাপাশা, আলফাডাঙ্গা",
                lat: 23.2785,
                lng: 89.6980,
                phone: "০১৭৮৮-৯৯২২৩৩",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["পিঁয়াজু"],
                hasBiriyani: true
            },
            
            // পাকুড়িয়া (আলফাডাঙ্গার অন্তর্গত)
            {
                id: 11,
                name: "পাকুড়িয়া কেন্দ্রীয় জামে মসজিদ",
                area: "পাকুড়িয়া",
                address: "পাকুড়িয়া বাজার, আলফাডাঙ্গা",
                lat: 23.2650,
                lng: 89.6950,
                phone: "০১৭১২-৩৩৪৪৫৫",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["খেজুর", "জিলাপি"],
                hasBiriyani: true
            },
            {
                id: 12,
                name: "পাকুড়িয়া পশ্চিম পাড়া জামে মসজিদ",
                area: "পাকুড়িয়া",
                address: "পাকুড়িয়া পশ্চিম পাড়া, আলফাডাঙ্গা",
                lat: 23.2635,
                lng: 89.6920,
                phone: "০১৭৪৫-৬৭৮৯০১",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["ছোলা", "মুড়ি"],
                hasBiriyani: true
            },
            {
                id: 13,
                name: "পাকুড়িয়া পূর্ব পাড়া জামে মসজিদ",
                area: "পাকুড়িয়া",
                address: "পাকুড়িয়া পূর্ব পাড়া, আলফাডাঙ্গা",
                lat: 23.2665,
                lng: 89.6980,
                phone: "০১৯৯৯-১১২২৩৩",
                iftarTime: "সন্ধ্যা ৬:২১",
                biriyani: ["মুরগি বিরিয়ানি"],
                iftar: ["হালিম"],
                hasBiriyani: true
            }
        ];
        
        // ম্যাপ ভেরিয়েবল
        let map;
        let markers = [];
        let activeArea = 'faridpur';
        
        // ম্যাপ ইনিশিয়ালাইজ
        function initMap() {
            // ফরিদপুরের সেন্টার
            map = L.map('map').setView([23.5961, 89.8425], 12);
            
            // ওপেনস্ট্রিটম্যাপ টাইলস (গুগল ম্যাপের মত ফ্রি)
            L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
                attribution: '©OpenStreetMap, ©CartoDB'
            }).addTo(map);
            
            // মসজিদগুলো ম্যাপে দেখাও
            showMosques('faridpur');
            
            // সাইডবার আপডেট করো
            updateSidebar('faridpur');
        }
        
        // মসজিদ দেখানোর ফাংশন
        function showMosques(area) {
            // পুরনো মার্কার সরাও
            markers.forEach(marker => map.removeLayer(marker));
            markers = [];
            
            // এলাকা অনুযায়ী ফিল্টার করো
            let filtered = mosques;
            if (area !== 'all') {
                filtered = mosques.filter(m => m.area === area);
            }
            
            // নতুন মার্কার যোগ করো
            filtered.forEach(mosque => {
                // কাস্টম আইকন
                const icon = L.divIcon({
                    html: `<div style="background: #1a5f4b; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; border: 3px solid #ffd700; box-shadow: 0 4px 10px rgba(0,0,0,0.3);">
                        <i class="fas fa-mosque" style="color: #ffd700; font-size: 20px;"></i>
                    </div>`,
                    className: 'custom-marker',
                    iconSize: [40, 40],
                    iconAnchor: [20, 40],
                    popupAnchor: [0, -40]
                });
                
          
