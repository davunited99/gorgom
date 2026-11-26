<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Data Center Inventory Dashboard</title>
    <style>
        :root {
            --primary-color: #2c3e50;
            --accent-color: #3498db;
            --bg-color: #f4f7f6;
            --card-bg: #ffffff;
            --text-color: #333;
            --border-color: #e0e0e0;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            margin: 0;
            padding: 20px;
            color: var(--text-color);
        }

        .container {
            max-width: 1300px;
            margin: 0 auto;
            background: var(--card-bg);
            padding: 25px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.08);
        }

        h2 {
            text-align: center;
            color: var(--primary-color);
            margin-bottom: 25px;
        }

        /* DC Navigation Tabs Utama - Teks Berjejer Tanpa Kotak + Panah Segitiga */
        .dc-tabs {
            display: flex;
            flex-wrap: wrap;
            gap: 24px;
            justify-content: center;
            margin-bottom: 20px;
            border-bottom: 1px solid var(--border-color);
            padding-bottom: 10px;
        }

        .dc-tab-btn {
            background: transparent;
            border: none;
            border-bottom: 3px solid transparent;
            padding: 8px 4px;
            font-size: 14px;
            font-weight: 700;
            cursor: pointer;
            color: var(--primary-color);
            display: inline-flex;
            align-items: center;
            gap: 6px;
            transition: all 0.2s ease-in-out;
        }

        .dc-tab-btn:hover {
            color: var(--accent-color);
        }

        .dc-tab-btn.active {
            color: var(--accent-color);
            border-bottom: 3px solid var(--accent-color);
        }

        .arrow-icon {
            font-size: 10px;
            transition: transform 0.2s ease;
            display: inline-block;
        }

        .dc-tab-btn.active .arrow-icon {
            transform: rotate(180deg);
        }

        /* Container Sub-Menu di Bawah Tombol Utama (Tanpa Kotak Pembungkus) */
        .sub-tabs-container {
            display: none;
            flex-wrap: wrap;
            gap: 8px;
            justify-content: center;
            margin-bottom: 25px;
            padding-bottom: 15px;
            border-bottom: 1px solid var(--border-color);
        }

        .sub-tabs-container.show {
            display: flex;
        }

        .sub-tab-btn {
            background-color: #ffffff;
            border: 1px solid var(--accent-color);
            padding: 7px 13px;
            font-size: 12px;
            font-weight: 600;
            cursor: pointer;
            border-radius: 5px;
            color: var(--accent-color);
            transition: all 0.2s ease-in-out;
        }

        .sub-tab-btn:hover {
            background-color: #e8f4fc;
        }

        .sub-tab-btn.active {
            background-color: var(--accent-color);
            color: white;
        }

        /* Layout Utama Full Width */
        .dashboard-layout {
            display: block;
        }

        .main-content {
            width: 100%;
        }

        /* Search & Info Bar */
        .toolbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            flex-wrap: wrap;
            gap: 15px;
        }

        .title-badge-wrapper {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        #currentDcTitle {
            font-size: 18px;
            font-weight: bold;
            color: var(--primary-color);
            margin: 0;
        }

        .device-count {
            background-color: #e8f4fc;
            color: var(--accent-color);
            padding: 4px 10px;
            border-radius: 12px;
            font-size: 12px;
            font-weight: 600;
        }

        .toolbar-actions {
            display: flex;
            align-items: center;
            gap: 10px;
            flex-wrap: wrap;
        }

        .search-box input {
            width: 320px;
            max-width: 100%;
            padding: 10px 15px;
            font-size: 14px;
            border: 1px solid var(--border-color);
            border-radius: 6px;
            outline: none;
            transition: border-color 0.2s;
            box-sizing: border-box;
        }

        .search-box input:focus {
            border-color: var(--accent-color);
        }

        /* Tombol + di sebelah kanan */
        .add-btn {
            background-color: var(--accent-color);
            color: #fff;
            border: none;
            width: 38px;
            height: 38px;
            border-radius: 6px;
            font-size: 22px;
            font-weight: bold;
            line-height: 1;
            cursor: pointer;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            transition: background-color 0.2s, transform 0.15s;
            box-shadow: 0 2px 6px rgba(52, 152, 219, 0.25);
        }

        .add-btn:hover {
            background-color: #2980b9;
        }

        .add-btn.active {
            background-color: #e74c3c;
            transform: rotate(45deg);
        }

        /* Panel Input Kolom Baru (Muncul saat tanda + diklik) */
        .add-form-container {
            display: none;
            background-color: #f8fbfe;
            border: 1px solid #cce5f6;
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 20px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.04);
        }

        .add-form-container.show {
            display: block;
            animation: fadeIn 0.2s ease-in-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-5px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .add-form-header {
            font-size: 13px;
            font-weight: 700;
            color: var(--primary-color);
            margin-bottom: 10px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 8px;
        }

        .add-form-hint {
            font-size: 12px;
            font-weight: normal;
            color: #666;
        }

        .add-form-grid {
            display: grid;
            grid-template-columns: 2fr 1.5fr 0.9fr 1.2fr 1.5fr auto;
            gap: 10px;
            align-items: center;
        }

        @media (max-width: 900px) {
            .add-form-grid {
                grid-template-columns: 1fr 1fr;
            }
        }

        .add-form-grid input {
            width: 100%;
            padding: 9px 12px;
            font-size: 13px;
            border: 1px solid #cbd5e0;
            border-radius: 5px;
            outline: none;
            box-sizing: border-box;
            background: #fff;
        }

        .add-form-grid input:focus {
            border-color: var(--accent-color);
            box-shadow: 0 0 0 2px rgba(52, 152, 219, 0.15);
        }

        .save-row-btn {
            background-color: #27ae60;
            color: white;
            border: none;
            padding: 9px 16px;
            border-radius: 5px;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            white-space: nowrap;
            transition: background-color 0.2s;
        }

        .save-row-btn:hover {
            background-color: #219150;
        }

        /* Table Styling */
        .table-responsive {
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background-color: #fff;
        }

        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid var(--border-color);
            font-size: 14px;
        }

        th {
            background-color: var(--accent-color);
            color: white;
            font-weight: 600;
        }

        tr:hover {
            background-color: #f8fafb;
        }

        .no-data {
            text-align: center;
            padding: 30px;
            color: #7f8c8d;
            font-style: italic;
        }

        .rack-badge {
            display: inline-block;
            background-color: #edf2f7;
            color: #2d3748;
            padding: 3px 8px;
            border-radius: 4px;
            font-weight: 600;
            font-size: 12px;
        }

        /* Container Gambar Layout */
        .layout-view-container {
            display: none;
            text-align: center;
            padding: 15px;
            background: #ffffff;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            overflow-x: auto;
        }

        .layout-view-container img,
        .layout-view-container svg {
            max-width: 100%;
            height: auto;
            border-radius: 6px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.06);
        }

        .layout-caption {
            margin-top: 12px;
            font-size: 13px;
            color: #666;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Data Center Inventory Dashboard</h2>
    
    <!-- Navigasi Data Center Utama (Tulisan Berjejer Tanpa Kotak + Panah Segitiga) -->
    <div class="dc-tabs" id="dcTabs">
        <button class="dc-tab-btn active" onclick="switchDC('NEXCYBER2', this)">
            <span>NEX CYBER 2</span><span class="arrow-icon">▼</span>
        </button>
        <button class="dc-tab-btn" onclick="switchDC('CYBER1', this)">
            <span>CYBER 1</span><span class="arrow-icon">▼</span>
        </button>
        <button class="dc-tab-btn" onclick="switchDC('IDCDUREN3', this)">
            <span>IDC DUREN 3</span><span class="arrow-icon">▼</span>
        </button>
        <button class="dc-tab-btn" onclick="switchDC('CSF', this)">
            <span>CSF</span><span class="arrow-icon">▼</span>
        </button>
        <button class="dc-tab-btn" onclick="switchDC('MTEN', this)">
            <span>MTEN</span><span class="arrow-icon">▼</span>
        </button>
        <button class="dc-tab-btn" onclick="switchDC('GABUNGAN', this)">
            <span>Gabungan beberapa DC</span><span class="arrow-icon">▼</span>
        </button>
    </div>

    <!-- Container Sub-Menu Horizontal di Bawah Tab Utama -->
    <div class="sub-tabs-container" id="subTabsContainer">
        <!-- Tombol sub-menu akan di-generate otomatis via JS -->
    </div>

    <!-- Layout Utama -->
    <div class="dashboard-layout">
        <!-- Konten Utama (Toolbar & Tabel) -->
        <div class="main-content">
            <div class="toolbar">
                <div class="title-badge-wrapper">
                    <h3 id="currentDcTitle">NEX CYBER 2</h3>
                    <span class="device-count" id="deviceCount">0 perangkat</span>
                </div>
                <div class="toolbar-actions">
                    <div class="search-box">
                        <input type="text" id="searchInput" onkeyup="filterTable()" placeholder="Cari hostname, device, rack, atau lokasi...">
                    </div>
                    <button type="button" class="add-btn" id="toggleAddBtn" onclick="toggleAddForm()" title="Tambah Perangkat Baru">+</button>
                </div>
            </div>

            <!-- Form Input Kolom Baru (Muncul saat tombol + diklik) -->
            <div class="add-form-container" id="addFormContainer">
                <div class="add-form-header">
                    <span>Tambah Data Baru ke: <strong id="targetCategoryLabel">NEX CYBER 2</strong></span>
                </div>
                <div class="add-form-grid">
                    <input type="text" id="newHostname" placeholder="Hostname" onkeydown="handleAddKeyPress(event)">
                    <input type="text" id="newDevice" placeholder="Device" onkeydown="handleAddKeyPress(event)">
                    <input type="text" id="newDimensi" placeholder="Dimensi" onkeydown="handleAddKeyPress(event)">
                    <input type="text" id="newRack" placeholder="Rack" onkeydown="handleAddKeyPress(event)">
                    <input type="text" id="newLocation" placeholder="Location" onkeydown="handleAddKeyPress(event)">
                    <button type="button" class="save-row-btn" onclick="submitNewDevice()">Simpan</button>
                </div>
            </div>

            <!-- Container Gambar Layout Cyber 1 Lantai 10 -->
            <div class="layout-view-container" id="layoutViewContainer">
                <img id="cyber1LayoutImg" src="layout cyber1.pdf.jpg" alt="Layout Cyber 1 Lantai 10" onerror="this.style.display='none'; document.getElementById('cyber1LayoutSvg').style.display='inline-block';">

                <svg id="cyber1LayoutSvg" style="display:none; background:#fff; border:1px solid #333;" viewBox="0 0 1320 650" xmlns="http://www.w3.org/2000/svg">
                    <defs>
                        <pattern id="greenHatch" width="8" height="8" patternTransform="rotate(45 0 0)" patternUnits="userSpaceOnUse">
                            <line x1="0" y1="0" x2="0" y2="8" stroke="#a3d199" stroke-width="3" />
                        </pattern>
                    </defs>

                    <rect x="0" y="0" width="1320" height="8" fill="#a6a6a6" />
                    <rect x="20" y="10" width="95" height="105" fill="#fff" stroke="#888" stroke-width="1.2" />
                    <text x="67" y="66" font-family="Segoe UI, Arial" font-size="14" text-anchor="middle" fill="#222">AC Blower</text>

                    <text x="275" y="42" font-family="Segoe UI, Arial" font-size="14" fill="#e74c3c" text-anchor="end">Wing Kiri</text>
                    <path d="M 288 22 L 281 22 L 281 52 L 288 52" fill="none" stroke="#222" stroke-width="1.5" />

                    <rect x="308" y="22" width="5" height="430" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <rect x="313" y="22" width="335" height="5" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <rect x="653" y="22" width="557" height="5" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <text x="480" y="48" font-family="Segoe UI, Arial" font-size="15" text-anchor="middle" fill="#222">Tray</text>

                    <rect x="675" y="27" width="4" height="520" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <rect x="838" y="27" width="4" height="520" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <rect x="1205" y="22" width="5" height="528" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>
                    <rect x="653" y="545" width="557" height="5" fill="#a6a6a6" stroke="#666" stroke-width="0.5"/>

                    <rect x="323" y="168" width="330" height="380" fill="#fff" stroke="#666" stroke-width="1"/>
                    <path d="M 653 365 L 613 365 A 40 40 0 0 0 653 405" fill="none" stroke="#666" stroke-width="1"/>
                    <path d="M 615 548 L 615 588 A 40 40 0 0 0 655 548" fill="none" stroke="#666" stroke-width="1"/>

                    <line x1="705" y1="0" x2="705" y2="650" stroke="#999" stroke-width="1" stroke-dasharray="5,5"/>

                    <g stroke="#555" stroke-width="1" fill="#fff">
                        <rect x="70" y="185" width="110" height="48"/>
                        <path d="M 70 185 L 22 185 A 48 48 0 0 0 70 233" fill="none"/>
                        <rect x="70" y="248" width="110" height="48"/>
                        <path d="M 70 248 L 22 248 A 48 48 0 0 0 70 296" fill="none"/>
                        <rect x="70" y="311" width="110" height="48"/>
                        <path d="M 70 311 L 22 311 A 48 48 0 0 0 70 359" fill="none"/>
                    </g>
                    <g font-family="Segoe UI, Arial" font-size="13" fill="#222" text-anchor="middle">
                        <text x="125" y="209" transform="rotate(90 125,209)">DWDM</text>
                        <text x="125" y="272" transform="rotate(90 125,272)">DWDM</text>
                        <text x="125" y="335" transform="rotate(90 125,335)">DWDM</text>
                    </g>

                    <g stroke="#555" stroke-width="1" fill="#fff">
                        <rect x="192" y="185" width="106" height="48"/>
                        <path d="M 298 233 L 346 233 A 48 48 0 0 0 298 185" fill="none"/>
                        <rect x="192" y="248" width="106" height="48"/>
                        <path d="M 298 296 L 346 296 A 48 48 0 0 0 298 248" fill="none"/>
                        <rect x="192" y="311" width="106" height="48"/>
                        <path d="M 298 359 L 346 359 A 48 48 0 0 0 298 311" fill="none"/>
                    </g>
                    <g font-family="Segoe UI, Arial" font-size="13" fill="#222" text-anchor="middle">
                        <text x="245" y="209" transform="rotate(90 245,209)">Recti</text>
                        <text x="245" y="272" transform="rotate(90 245,272)">DWDM</text>
                        <text x="252" y="335" transform="rotate(90 252,335)">OTB</text>
                        <text x="236" y="335" transform="rotate(90 236,335)">Tline</text>
                    </g>

                    <g stroke="#555" stroke-width="1" fill="#fff">
                        <rect x="92" y="465" width="52" height="115"/>
                        <path d="M 144 465 L 144 415 A 50 50 0 0 0 94 465" fill="none"/>
                    </g>
                    <text x="118" y="518" font-family="Segoe UI, Arial" font-size="13" fill="#222" text-anchor="middle" transform="rotate(180 118,518)">DWDM</text>

                    <g stroke="#555" stroke-width="1" fill="#fff" font-family="Segoe UI, Arial" font-size="13">
                        <rect x="720" y="50" width="105" height="46"/><path d="M 720 50 L 680 50 A 40 46 0 0 0 680 96" fill="none"/><text x="772" y="73" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,73)">C01</text>
                        <rect x="720" y="106" width="105" height="46"/><path d="M 720 106 L 680 106 A 40 46 0 0 0 680 152" fill="none"/><text x="772" y="129" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,129)">C02</text>
                        <rect x="720" y="162" width="105" height="46"/><path d="M 720 162 L 680 162 A 40 46 0 0 0 680 208" fill="none"/><text x="772" y="185" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,185)">C03</text>
                        <rect x="720" y="218" width="105" height="46"/><path d="M 720 218 L 680 218 A 40 46 0 0 0 680 264" fill="none"/><text x="772" y="241" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,241)">C04</text>
                        <rect x="720" y="274" width="105" height="46"/><path d="M 720 274 L 680 274 A 40 46 0 0 0 680 320" fill="none"/><text x="772" y="297" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,297)">C05</text>
                        <rect x="720" y="330" width="105" height="46"/><path d="M 720 330 L 680 330 A 40 46 0 0 0 680 376" fill="none"/><text x="772" y="353" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,353)">C06</text>
                        <rect x="720" y="386" width="105" height="46"/><path d="M 720 386 L 680 386 A 40 46 0 0 0 680 432" fill="none"/><text x="772" y="409" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,409)">C07</text>
                        <rect x="720" y="442" width="105" height="46"/><path d="M 720 442 L 680 442 A 40 46 0 0 0 680 488" fill="none"/><text x="772" y="465" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 772,465)">C08</text>
                    </g>

                    <g stroke="#555" stroke-width="1" fill="#fff" font-family="Segoe UI, Arial" font-size="13">
                        <rect x="880" y="45" width="105" height="46"/><path d="M 985 91 L 1025 91 A 40 46 0 0 0 1025 45" fill="none"/><text x="932" y="68" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,68)">B03</text>
                        <rect x="880" y="103" width="105" height="46"/><path d="M 985 149 L 1025 149 A 40 46 0 0 0 1025 103" fill="none"/><text x="932" y="126" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,126)">B04</text>
                        <rect x="880" y="161" width="105" height="46"/><path d="M 985 207 L 1025 207 A 40 46 0 0 0 1025 161" fill="none"/><text x="932" y="184" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,184)">B05</text>
                        <rect x="880" y="219" width="105" height="46"/><path d="M 985 265 L 1025 265 A 40 46 0 0 0 1025 219" fill="none"/><text x="932" y="242" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,242)">B06</text>
                        <rect x="880" y="277" width="105" height="46"/><path d="M 985 323 L 1025 323 A 40 46 0 0 0 1025 277" fill="none"/><text x="932" y="300" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,300)">B07</text>
                        <rect x="880" y="335" width="105" height="46"/><path d="M 985 381 L 1025 381 A 40 46 0 0 0 1025 335" fill="none"/><text x="932" y="358" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,358)">B08</text>
                        <rect x="880" y="393" width="105" height="46"/><path d="M 985 439 L 1025 439 A 40 46 0 0 0 1025 393" fill="none"/><text x="932" y="416" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,416)">B09</text>
                        <rect x="880" y="451" width="105" height="46"/><path d="M 985 497 L 1025 497 A 40 46 0 0 0 1025 451" fill="none"/><text x="932" y="474" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 932,474)">B10</text>
                    </g>

                    <g stroke="#555" stroke-width="1" fill="#fff" font-family="Segoe UI, Arial" font-size="13">
                        <rect x="1075" y="48" width="105" height="46"/><path d="M 1075 48 L 1035 48 A 40 46 0 0 0 1035 94" fill="none"/><text x="1127" y="71" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,71)">A01</text>
                        <rect x="1075" y="104" width="105" height="46"/><path d="M 1075 104 L 1035 104 A 40 46 0 0 0 1035 150" fill="none"/><text x="1127" y="127" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,127)">A02</text>
                        <rect x="1075" y="160" width="105" height="46"/><path d="M 1075 160 L 1035 160 A 40 46 0 0 0 1035 206" fill="none"/><text x="1127" y="183" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,183)">A03</text>
                        <rect x="1075" y="216" width="105" height="46"/><path d="M 1075 216 L 1035 216 A 40 46 0 0 0 1035 262" fill="none"/><text x="1127" y="239" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,239)">A04</text>
                        <rect x="1075" y="272" width="105" height="46"/><path d="M 1075 272 L 1035 272 A 40 46 0 0 0 1035 318" fill="none"/><text x="1127" y="295" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,295)">A05</text>
                        <rect x="1075" y="328" width="105" height="46"/><path d="M 1075 328 L 1035 328 A 40 46 0 0 0 1035 374" fill="none"/><text x="1127" y="351" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,351)">A06</text>
                        <rect x="1075" y="384" width="105" height="46"/><path d="M 1075 384 L 1035 384 A 40 46 0 0 0 1035 430" fill="none"/><text x="1127" y="407" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,407)">A07</text>
                        <rect x="1075" y="440" width="105" height="46"/><path d="M 1075 440 L 1035 440 A 40 46 0 0 0 1035 486" fill="none"/><text x="1127" y="463" fill="#222" stroke="none" text-anchor="middle" transform="rotate(90 1127,463)">A08</text>
                    </g>

                    <rect x="1238" y="255" width="75" height="175" fill="url(#greenHatch)" stroke="#888" stroke-width="1"/>
                    <text x="1275" y="392" font-family="Segoe UI, Arial" font-size="14" fill="#222" text-anchor="middle">Lemari</text>
                </svg>

                <div class="layout-caption">Denah Tata Letak Rack & Perangkat — Cyber 1 Lantai 10 (Row A, Row B, Row C, OTB/T-Line, DWDM & Wing Kiri)</div>
            </div>

            <!-- Tabel Data -->
            <div class="table-responsive" id="tableContainer">
                <table id="deviceTable">
                    <thead>
                        <tr id="tableHeaders">
                            <th>Hostname</th>
                            <th>Device</th>
                            <th>Dimensi</th>
                            <th>Rack</th>
                            <th>Location</th>
                        </tr>
                    </thead>
                    <tbody id="tableBody">
                        <!-- Data akan dimuat melalui JavaScript -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</div>

<script>
    // Database Data Center Lengkap
    const dataCenters = {
        NEXCYBER2: [
            { hostname: "JKCB2-01-1-NAP-SW1", col2: "CISCO CATALYST 3850-48XS", col3: "1U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "JKCB2-01-1-MTR-RT1", col2: "CISCO NCS 5501", col3: "1U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-NAP-RT1-CYB2", col2: "CISCO ASR 9000", col3: "9U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-DM10-CYB2", col2: "HUAWEI NE8000", col3: "1U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-KYB-132-P2-CYB2", col2: "HUAWEI NE 8000 F2A", col3: "2U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-KYB-132-SC1-CYB2", col2: "HUAWEI NE 8000 F2C", col3: "2U", rack: "ND 1 Rack 01", location: "Cyber2 - NEX Data Center" },
            { hostname: "CISCO 8201", col2: "CISCO 8201", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-KYB-132-SW10-CYB2", col2: "HUAWEI S5731", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JKT-02-1-LT1-CYBER2", col2: "OLT ZTE 320", col3: "2U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-RR1-CYB2", col2: "XRV 9000", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "RT-PE-JTPM-CLOUD-01-CYB2", col2: "CISCO 8500", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JKTCB2-01-1-MTR-RT2", col2: "CISCO NCS 55A1", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-DM15-CYB2", col2: "HUAWEI NE8000", col3: "1U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-P1-CYB2", col2: "CISCO NCS 5504", col3: "7U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "JK-JKT-02-NAP-RT2-CYB2", col2: "ASR 9903", col3: "3U", rack: "ND 1 Rack 02", location: "Cyber2 - NEX Data Center" },
            { hostname: "CBNN-SW-ALU-OS6860-CYBER2-LT2", col2: "ALCATEL 6860", col3: "-", rack: "RACK CBNN", location: "MMR-ND1-CYBER2-Lt2" },
            { hostname: "CYB2-ALU-6450-24-MAERQUEE", col2: "ALCATEL 6450", col3: "-", rack: "RACK CBNN", location: "MMR-ND1-CYBER2-Lt2" },
            { hostname: "CYB2-SW-ALU-OS6224-CBNN-LT2", col2: "ALCATEL 6224", col3: "-", rack: "RACK CBNN", location: "MMR-ND1-CYBER2-Lt2" },
            { hostname: "CYB2-SW-ALU-OS6250-CBNN-LT2", col2: "ALCATEL 6250", col3: "-", rack: "RACK CBNN", location: "MMR-ND1-CYBER2-Lt2" },
            { hostname: "JK-JKT-15-SW3-CYB2", col2: "ALU OS6900-X20", col3: "1U", rack: "NH1-05-01", location: "Half Rack ND1" },
            { hostname: "JK-JKT-02-SW5-CYB2", col2: "HUAWEI S5736", col3: "1U", rack: "NH1-05-01", location: "Half Rack ND1" },
            { hostname: "OTB 1 (to OTB 9 ND4)", col2: "144 Core LC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 2 (to OTB 9 ND4)", col2: "96 Core SC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 3 (to IDC, JTPM NTT)", col2: "96 Core SC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 4 (to Overlay dan RB Margahayu)", col2: "96 Core SC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 5 (to MMR Colo CBN)", col2: "144 Core LC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 6 (to ND4)", col2: "144 Core LC", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "OTB 9 (NEW)", col2: "-", col3: "-", rack: "OTB ND1", location: "Cage ND1" },
            { hostname: "JKCB2-01-33-MTR-PE5", col2: "ASR 920", col3: "-", rack: "Ruang Server CBN", location: "Kantor CBN Lt 33" },
            { hostname: "JKCB2-01-01-MTR-DM1", col2: "ASR 9901", col3: "2U", rack: "CRF 01-01", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-01-MTR-PE1", col2: "ASR 920", col3: "1U", rack: "CRF 01-01", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-05-MTR-PE2", col2: "ASR 920", col3: "1U", rack: "CRF 01-05", location: "COLO CBN - CYBER 2" },
            { hostname: "JK-JKT-02-C1-CYB2", col2: "NE8000 F1A-8H20Q", col3: "1U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-05-MTR-DM2", col2: "ASR 9901", col3: "2U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-05-MTR-PE3", col2: "ASR 920", col3: "1U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-01-MTR1-C1", col2: "NCS5501", col3: "1U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-06-MTR1-C2", col2: "NCS5501", col3: "1U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JK-JKT-02-DM13-CYB2", col2: "NE40E", col3: "1U", rack: "CRF 01-06", location: "COLO CBN - CYBER 2" },
            { hostname: "JKCB2-01-10-MTR-PE4", col2: "ASR 920", col3: "1U", rack: "CRF 02-10", location: "COLO CBN - CYBER 2" },
            { hostname: "JK-JKT-BNG-RT1-CYB2", col2: "-", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "IGW-RT1", col2: "-", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "JK-JKT-02-SC2-CYB2", col2: "HUAWEI NE8000 F2A", col3: "2U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "MS", col2: "CISCO 9300", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "MS", col2: "CISCO 9300", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "CISCO FIREWALL", col2: "CISCO 9300", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "RT-PE-JTPM CLOUD 01 CYB2", col2: "-", col3: "1U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-DM4-CYB2", col2: "NE8000 F2D", col3: "3U", rack: "ND4-01", location: "CAGE ND4" },
            { hostname: "JK-JKT-02-P3-CYB2", col2: "CISCO NCS 5504", col3: "7U", rack: "ND4-02", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-DM5-CYB2", col2: "HUAWEI NE8000 F2D", col3: "3U", rack: "ND4-02", location: "CAGE ND4" },
            { hostname: "SPACE 11U", col2: "-", col3: "11U", rack: "ND4-03", location: "CAGE ND4" },
            { hostname: "JK-JKT-02-DM3-CYB2", col2: "CISCO ASR 9300", col3: "4U", rack: "ND4-03", location: "CAGE ND4" },
            { hostname: "JK-JKT-DW4-CYB2-2-1-D", col2: "HUAWEI DWDM", col3: "4U", rack: "ND4-03", location: "CAGE ND4" },
            { hostname: "DW4-CYB2 NE ID 9-48", col2: "HUAWEI DWDM", col3: "4U", rack: "ND4-03", location: "CAGE ND4" },
            { hostname: "SPACE 22U", col2: "-", col3: "22U", rack: "ND4-04", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-NAP-P1-CYB2", col2: "HUAWEI NE8000 F2C", col3: "2U", rack: "ND 4 Rack 04", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-NAP-RT1-CYB2", col2: "HUAWEI NE8000 X4", col3: "4U", rack: "ND 4 Rack 04", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-NAP-P2-CYB2", col2: "HUAWEI NE8000 F2C", col3: "2U", rack: "ND 4 Rack 05", location: "CAGE ND4" },
            { hostname: "JK-KYB-132-NAP-RT2-CYB2", col2: "HUAWEI NE8000 X4", col3: "4U", rack: "ND 4 Rack 06", location: "CAGE ND4" },
            { hostname: "OTB 9 (To ND1)", col2: "LC 144 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 8 (KOSONG)", col2: "SC 96 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 7 (to DWDM UJB Karawang- Margahayu)", col2: "SC 96 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 6 (to DWDM Ancol)", col2: "SC 96 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 6 (to Maspion)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 6 (to R8)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 6 (Jatinegara NDC Moratel)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 5 (Kosong)", col2: "SC 96 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 4 (Core 1-48 to BITERA,)", col2: "SC 96 Core", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 4 (Core 49-72 to Equinix)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 4 (Core 73- 96 to NTT CSF)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 3 (Belum ada alokasi)", col2: "144 Core LC", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 2 (To ND1)", col2: "144 Core LC", col3: "-", rack: "OTB ND4", location: "Cage ND4" },
            { hostname: "OTB 1 (To MMR Colo CBN)", col2: "-", col3: "-", rack: "OTB ND4", location: "Cage ND4" }
        ],
        CYBER1: {
            APJII_OMNI: [
                { hostname: "JK-JKT-15-SW12-X72-CYB1", col2: "ALCATEL OS6900-X72", col3: "1U", rack: "RACK Z1", location: "APJII CYBER 1" },
                { hostname: "JKAPJIICYB1-01-10-MTR-PE90", col2: "ASR-920-24SZ", col3: "1U", rack: "RACK Z1", location: "APJII CYBER 1" },
                { hostname: "APJII-SW-ALU-6224-1-RAK-Z1", col2: "ALCATEL 6224", col3: "1U", rack: "RACK Z1", location: "APJII CYBER 1" },
                { hostname: "T-Line APJII <> Keluar APJII", col2: "T-Line", col3: "-", rack: "RACK Z1", location: "APJII CYBER 1" },
                { hostname: "JK-KYB-134-SW24-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK i2", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW15-CYB1", col2: "ALCATEL 6900-X20", col3: "1U", rack: "RACK i2", location: "APJII CYBER 1" },
                { hostname: "T-Line Apjji <> T-Line Lt 10", col2: "T-Line Port LC", col3: "2U", rack: "RACK i2", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-DM11-CYB1", col2: "Huawei NE40E", col3: "1U", rack: "RACK i2", location: "APJII CYBER 1" },
                { hostname: "NEW T-Line Apjji <> T-Line Lt 10", col2: "T-Line Port LC", col3: "2U", rack: "RACK i2", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW8-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK U2", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW9-CYB1", col2: "Alcatel OS6900-X72", col3: "1U", rack: "RACK U2", location: "APJII CYBER 1" },
                { hostname: "JKCB1-01-1-NAP-PE1", col2: "CISCO NCS 5501", col3: "-", rack: "RACK T2", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW17-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK X4", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW18-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK X4", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW16-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK M1", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW13-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK W5", location: "MMR APJII RACK W5 - Lantai 1" },
                { hostname: "JK-JKT-15-SW14-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK W5", location: "MMR APJII RACK W5 - Lantai 1" },
                { hostname: "JK-JKT-15-SW5-CYB1", col2: "Alcatel OS6860E-24", col3: "1U", rack: "RACK MDA (Z5)", location: "New MMR APJII - Lantai 1" },
                { hostname: "JK-JKT-15-SW10-CYB1", col2: "Alcatel OS6900-X72", col3: "1U", rack: "RACK MDA (Z5)", location: "New MMR APJII - Lantai 1" },
                { hostname: "JK-JKT-15-SW11-CYB1", col2: "Alcatel OS6860E-24", col3: "1U", rack: "RACK W4", location: "New MMR APJII - Lantai 1" },
                { hostname: "JK-JKT-15-SW19-CYB1", col2: "Huawei S6730", col3: "1U", rack: "RACK E7", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW20-CYB1", col2: "Huawei S6750", col3: "1U", rack: "RACK E7", location: "APJII CYBER 1" },
                { hostname: "JK-KYB-134-SW22-CYB1", col2: "S6730-H48X6C", col3: "1U", rack: "RACK K7", location: "APJII CYBER 1" },
                { hostname: "JK-KYB-134-SW23-CYB1", col2: "S6730-H48X6C", col3: "1U", rack: "RACK K7", location: "APJII CYBER 1" },
                { hostname: "JK-KYB-134-SW25-CYB1", col2: "Huawei CE 6730", col3: "1U", rack: "Rack P7", location: "APJII CYBER 1" },
                { hostname: "JK-KYB-134-SW26-CYB1", col2: "Huawei CE 6730", col3: "1U", rack: "Rack P7", location: "APJII CYBER 1" },
                { hostname: "JK-JKT-15-SW21-CYB1", col2: "S6730-H48X6C", col3: "1U", rack: "Room data hall B-19-1", location: "DC Omni CYBER 1 - Lantai 2" }
            ],
            MMR_LT7: [
                { hostname: "JKIDCCYB1-01-03-MTR-PE89", col2: "ASR-920-24SZ", col3: "1U", rack: "MMR Rack 6", location: "IDC CYBER 1 - Lantai 7" },
                { hostname: "IDCCYB1-SW-ALU-6224-LT7", col2: "ALCATEL 6224", col3: "1U", rack: "MMR Rack 6", location: "IDC CYBER 1 - Lantai 7" },
                { hostname: "JK-KYB-134-SW7-CYB1", col2: "Huawei S5736", col3: "1U", rack: "MMR Rack 6", location: "IDC CYBER 1 - Lantai 7" },
                { hostname: "IDCCYB1-SW-ALU-6450-24-LT7", col2: "ALCATEL 6450-24", col3: "1U", rack: "MMR Rack 3", location: "IDC CYBER 1 - Lantai 7" }
            ],
            LT10_RACK_A: [
                { hostname: "Perangkat JPTM", col2: "Switch Alcatel", col3: "1U", rack: "A01", location: "CYBER1-LT10" },
                { hostname: "Server CDN Wangsu", col2: "Huawei 6865E", col3: "1U", rack: "A02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Wangsu", col2: "Switch H3C S5500 Series", col3: "1U", rack: "A02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Wangsu", col2: "Server Wangsu", col3: "4 RU", rack: "A02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Wangsu", col2: "Server Wangsu", col3: "4 RU", rack: "A03", location: "CYBER1-LT10" },
                { hostname: "Server CDN Wangsu", col2: "Server Wangsu", col3: "4 RU", rack: "A04", location: "CYBER1-LT10" },
                { hostname: "Server CDN", col2: "Server Dell", col3: "4 RU", rack: "A04", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-NAP-RT2-CYB1", col2: "Huawei NE8000 F2D", col3: "2U", rack: "A05", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-DM8-CYB1", col2: "Cisco ASR-920", col3: "3U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-PE7-CYB1", col2: "NE8000 M1C", col3: "1U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-C3-CYB1", col2: "NE8000 F1A", col3: "1U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "JKCB1-01-10-MTR-C4", col2: "NCS 5501", col3: "1U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "JKCB1-01-10-MTR-C3 wil dismantle", col2: "NCS 5501", col3: "1U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "Space kosong 6 RU", col2: "-", col3: "6U", rack: "A.06", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-NAP-RT1-CYB1", col2: "Huawei NE8000 F2A", col3: "2U", rack: "A.07", location: "CYBER1-LT10" },
                { hostname: "Device CBN Non Aktiv (3x)", col2: "-", col3: "1U", rack: "A.07", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-DM4-CYB1", col2: "Huawei NE8000 F2A", col3: "2U", rack: "A.08", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-P1-CYB1", col2: "Huawei NE8000 F2C", col3: "2U", rack: "A.08", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-SC1-CYB1", col2: "Huawei NE8000 X4", col3: "6U", rack: "A.08", location: "CYBER1-LT10" }
            ],
            LT10_RACK_B: [
                { hostname: "Perangkat CBN", col2: "Perangkat CBN", col3: "7U", rack: "B01", location: "CYBER1-LT10" },
                { hostname: "Server CDN Edgenext", col2: "Switch Huawei CE 6857", col3: "1U", rack: "B02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Edgenext", col2: "DELL EMC R7525", col3: "2U", rack: "B02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Edgenext (2x)", col2: "INSPUR SA5212M5", col3: "2U", rack: "B02", location: "CYBER1-LT10" },
                { hostname: "Server CDN Edgenext (3x)", col2: "INSPUR SA5212M5", col3: "2U", rack: "B03", location: "CYBER1-LT10" },
                { hostname: "IPT FS Edgenet, SRV-CACHE 4", col2: "DELL EMC R7525", col3: "2U", rack: "B04", location: "CYBER1-LT10" },
                { hostname: "Rusak", col2: "DELL server power edge", col3: "2U", rack: "B04", location: "CYBER1-LT10" },
                { hostname: "IPT FS Edgenet, SRV-CACHE 5", col2: "Inspur server", col3: "2U", rack: "B04", location: "CYBER1-LT10" },
                { hostname: "IPT FS Edgenet, SRV-CACHE 10", col2: "INSPUR SA5212M5", col3: "2U", rack: "B05", location: "CYBER1-LT10" },
                { hostname: "IPT FS Edgenet, SRV-CACHE 11", col2: "DELL server power edge", col3: "2U", rack: "B05", location: "CYBER1-LT10" },
                { hostname: "Server Google GGC-FS, fiberstar-cgk3", col2: "Server DELL", col3: "2U", rack: "B06", location: "CYBER1-LT10" },
                { hostname: "Google GGC-FS server 1 (2x)", col2: "Google Server", col3: "2U", rack: "B06", location: "CYBER1-LT10" },
                { hostname: "JKCB1-01-1-NAP-P1", col2: "Cisco NCS 55A1", col3: "1U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "Google cache server 1 & 2", col2: "-", col3: "2U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "Router Google IPT", col2: "Cisco NCS 5001", col3: "1U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "Router Google IPT", col2: "Cisco NCS 55A1 24H", col3: "1U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "Switch Google", col2: "Switch Cisco NCS", col3: "1U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-SC2-CYB1", col2: "Huawei NE8000 F2A", col3: "3U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-SC3-CYB1", col2: "Huawei NE8000 F2A", col3: "3U", rack: "B.07", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-P2-CYB1", col2: "Cisco NCS-55A1-24H", col3: "1U", rack: "B.08", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-P1-CYB1", col2: "Cisco NCS 55A1 36H", col3: "1U", rack: "B.08", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-DM16-CYB1", col2: "Cisco ASR 9903", col3: "3U", rack: "B.08", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-SC3-CYB1", col2: "Huawei NE8000 F2A", col3: "3U", rack: "B.08", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-DM18-CYB1", col2: "Huawei NE40E", col3: "1U", rack: "B.08", location: "CYBER1-LT10" },
                { hostname: "JK-JKT-15-DM19-CYB1", col2: "Huawei NE40E", col3: "1U", rack: "B.08", location: "CYBER1-LT10" }
            ],
            LT10_RACK_C: [
                { hostname: "Rectifier B", col2: "Rectifier B", col3: "-", rack: "C.01", location: "CYBER1-LT10" },
                { hostname: "T-Line Antar Lt 10", col2: "T-Line", col3: "-", rack: "C.02", location: "CYBER1-LT10" },
                { hostname: "Perangkat CBN", col2: "Routerboard CBN", col3: "-", rack: "C.03", location: "CYBER1-LT10" },
                { hostname: "Perangkat CBN", col2: "MWDM CBN", col3: "-", rack: "C.03", location: "CYBER1-LT10" },
                { hostname: "JK-15-DW4-1-UAJB-F", col2: "DWDM FS", col3: "15U", rack: "C.04", location: "CYBER1-LT10" },
                { hostname: "Rack Kosong (C.05 - C.07)", col2: "-", col3: "-", rack: "C.05 - C.07", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-P2-CYB1", col2: "Huawei NE8000 F2C", col3: "2U", rack: "C.08", location: "CYBER1-LT10" },
                { hostname: "JK-KYB-134-SC2-CYB1", col2: "Huawei NE8000 X4", col3: "8U", rack: "C.08", location: "CYBER1-LT10" }
            ],
            LT10_OTB_B09: [
                { hostname: "OTB 6 T-Line to I2", col2: "LC", col3: "96 Core", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 5 T-Line to MTEN", col2: "LC", col3: "24 Core", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 4 T-Line to NTT", col2: "LC", col3: "18 Core", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 3 (Port 7-12 to Cyb1-Cyb2)", col2: "SC", col3: "-", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 3 (Port 91-96 to Cyb1-IDC Underlay/Overlay)", col2: "SC", col3: "-", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 3 (Port 7-8 to IDC)", col2: "SC", col3: "-", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 3 (Port 1,2,5,6 Cyb1-Cyb2 NAP)", col2: "SC", col3: "-", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "OTB 3 (Port 81-84 Cyb1-IDC NAP)", col2: "SC", col3: "-", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "Tieline to I2", col2: "LC", col3: "144 Core", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "Tieline Antar Lt 10", col2: "LC", col3: "144 Core", rack: "B.09", location: "CYBER1-LT10" },
                { hostname: "Tieline to I2 New", col2: "LC", col3: "144 Core", rack: "B.09", location: "CYBER1-LT10" }
            ],
            LT10_WING_KIRI: [
                { hostname: "Tieline Antar Lt 10", col2: "144 Port LC", col3: "144 Core", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 7 (LMS to PT KGN)", col2: "SC", col3: "12 Port", rack: "Wing Kiri", location: "OTB Wing Kiri Lt 10" },
                { hostname: "OTB 6 (T-Line to APJII Z1)", col2: "SC", col3: "96 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 6 to Kuningan Barat (Port 1-12 to Edge)", col2: "SC", col3: "96 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 6 to Kuningan Barat (Arah IDC & Bogor via DWDM)", col2: "SC", col3: "96 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 4", col2: "LC", col3: "24 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 3", col2: "SC", col3: "6 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 2 (to Pgascom Lt 8)", col2: "SC", col3: "24 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "OTB 1 (to APJII Z1 CBN)", col2: "SC", col3: "48 Port", rack: "Wing Kiri", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "Wing Kiri Samping Depan OTB", col2: "Rectifier 7U", col3: "Space 12U", rack: "Recti 7U", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "DW3-1 JAY Underlay", col2: "Huawei OSN 9800 M12", col3: "-", rack: "DWDM Huawei 1", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "NE-JK-Cyber1 / JKT-15-DW4-p2p Rempoa / Plaza Mandiri", col2: "HUAWEI DWDM", col3: "-", rack: "DWDM Huawei 2", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "Ultimate Cyb1 / DW2 ICI", col2: "DWDM", col3: "-", rack: "DWDM Huawei 3", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "DWDM Huawei 4", col2: "DWDM", col3: "-", rack: "DWDM Huawei 4", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "JKT-15-DW5-1-R8", col2: "Huawei OSN 9800 M12", col3: "-", rack: "DWDM Huawei 5", location: "Wing Kiri Cyber1 Lt 10" },
                { hostname: "Rectifier", col2: "Rectifier", col3: "-", rack: "Rack Telor Asin", location: "Wing Kiri Cyber1 Lt 10" }
            ]
        },
        IDCDUREN3: {
            ALL: [
                { hostname: "OTB 4", col2: "OTB", col3: "96 Core", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-NAP-P1", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-NAP-P2", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-RT2", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-NAP-RT1", col2: "ASR 9000", col3: "7U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-DM21-IDC3D", col2: "Huawei NE40E", col3: "1U", rack: "W165", location: "IDC Lantai 1" }
            ],
            W165: [
                { hostname: "OTB 4", col2: "OTB", col3: "96 Core", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-NAP-P1", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-NAP-P2", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-MTR-RT2", col2: "NCS 55A1", col3: "1U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JKIDC-01-1-NAP-RT1", col2: "ASR 9000", col3: "7U", rack: "W165", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-DM21-IDC3D", col2: "Huawei NE40E", col3: "1U", rack: "W165", location: "IDC Lantai 1" }
            ],
            TLINEW165: [
                { hostname: "OTB 3 OTB - W202", col2: "SC", col3: "48 Core", rack: "W165 Depan", location: "IDC Lantai 1" },
                { hostname: "OTB 2 - MMR Lt3", col2: "SC", col3: "96 Core", rack: "W165 Depan", location: "IDC Lantai 1" },
                { hostname: "OTB 1 - OTB Bangka", col2: "SC", col3: "96 Core", rack: "W165 Depan", location: "IDC Lantai 1" },
                { hostname: "OTB to HH CBN", col2: "SC", col3: "96 Core", rack: "W165 Depan", location: "IDC Lantai 1" },
                { hostname: "OTB 1 W165 to W171", col2: "LC", col3: "48 Core", rack: "W165 Belakang", location: "IDC Lantai 1" },
                { hostname: "OTB 2 W165 to W202", col2: "LC", col3: "48 Core", rack: "W165 Belakang", location: "IDC Lantai 1" },
                { hostname: "OTB 3 NEW W165 to W171", col2: "LC", col3: "96 Core", rack: "W165 Belakang", location: "IDC Lantai 1" },
                { hostname: "OTB 4 NEW W165 to MMR Lt3", col2: "LC", col3: "96 Core", rack: "W165 Belakang", location: "IDC Lantai 1" }
            ],
            W170: [
                { hostname: "DWDM Huawei osn 9800M12", col2: "DWDM Huawei osn 9800M12", col3: "Full rack", rack: "W170", location: "IDC D3 Lantai 1" },
                { hostname: "DWDM HUAWEIM9800 U16 HOSTNAME 3_IDC_E", col2: "DWDM HUAWEI 9800 U16 HOSTNAME 3_IDC_E", col3: "-", rack: "W170", location: "IDC D3 Lantai 1" },
                { hostname: "Optix OSN 9800 Huawei", col2: "Optix OSN 9800 Huawei", col3: "-", rack: "W170", location: "IDC D3 Lantai 1" },
                { hostname: "Optix OSN 9800 HUAWEI HOSTNAME A_IDC_8_0", col2: "Optix OSN 9800 HUAWEI HOSTNAME A_IDC_8_0", col3: "-", rack: "W170", location: "IDC D3 Lantai 1" }
            ],
            W171: [
                { hostname: "Recti", col2: "Recti", col3: "-", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "OTB Tline W171 - AU149", col2: "OTB", col3: "48 Core", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "OTB Tline W171 - W165", col2: "OTB", col3: "48 Core", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "OTB Tline W171 - W202", col2: "OTB", col3: "48 Core", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JKIDC-NAP-MTR-RT1-NEW", col2: "CISCO NCS 5501", col3: "1U", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-SC2-IDC3D > Change Name SC3-IDC", col2: "HUAWEI NE8000 F2A", col3: "2U", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JK-IKT-41-RR2-IDC3D", col2: "CISCO RR2", col3: "1U", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-DM14-IDC3D", col2: "CISCO ASR 9908", col3: "2U", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-P1-IDC3D", col2: "CISCO ASR 5504", col3: "7U", rack: "W171", location: "IDC Lantai 1" },
                { hostname: "JK-IKT-41-SW11-IDC3D", col2: "Alcatel 6450 10L", col3: "1U", rack: "W171 Belakang", location: "IDC Lantai 1" },
                { hostname: "JK-JKT-41-DM21-IDC3D", col2: "HUAWEI NE8000", col3: "1U", rack: "W171", location: "IDC Lantai 1" }
            ],
            OTBW171: [
                { hostname: "T-line W171 to AU149", col2: "LC to LC", col3: "OTB 48 Core", rack: "W171 Depan", location: "IDC Lantai 1" },
                { hostname: "Tline W171 to 165", col2: "LC to LC", col3: "OTB 48 Core", rack: "W171 Depan", location: "IDC Lantai 1" },
                { hostname: "Tine W171 to 202", col2: "LC to LC", col3: "OTB 96 Core", rack: "W171 Depan", location: "IDC Lantai 1" },
                { hostname: "OTB NEW W171 to W165", col2: "LC to LC", col3: "OTB 96 Core", rack: "W171 Belakang", location: "IDC Lantai 1" },
                { hostname: "OTB NEW W171 to MMR", col2: "LC to LC", col3: "OTB 96 Core", rack: "W171 Belakang", location: "IDC Lantai 1" },
                { hostname: "OTB NEW", col2: "LC to LC", col3: "OTB 96 Core", rack: "W171 Belakang", location: "IDC Lantai 1" }
            ],
            DWDMNOKIA: [
                { hostname: "DWDM NOKIA", col2: "DWDM NOKIA", col3: "Full rack", rack: "W201", location: "IDC Lantai 2" },
                { hostname: "-", col2: "-", col3: "-", rack: "W201", location: "IDC Lantai 2" },
                { hostname: "-", col2: "-", col3: "-", rack: "W201", location: "IDC Lantai 2" },
                { hostname: "-", col2: "-", col3: "-", rack: "W201", location: "IDC Lantai 2" }
            ],
            W202L2: [
                { hostname: "Recti", col2: "Recti", col3: "4U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "OTB1: W202 to MMR (Sc Sc)", col2: "OTB-Tline type SC-SC", col3: "48 Core", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "OTB2: W202 to W165 (Sc Sc)", col2: "OTB-Tline type SC-SC", col3: "48 Core", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JK-JKT 41-DM12-IDC3D", col2: "HUAWEI NE40E", col3: "1U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JK-IKT 41-P1-IDC3D", col2: "NCSSSA1", col3: "1U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JKT T-LINE W102 to MMR Lt3", col2: "OTB T-LINE", col3: "2U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JKT T-LINE W102 to W171", col2: "OTB T-LINE", col3: "2U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JKT-41-1-SW4-IDC DUREN3", col2: "ALCATEL OS6860-X20", col3: "1U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JK-IKT 41-SC1-IDC3D", col2: "HUAWEI NE8000 F2A", col3: "2U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JKT-41-1-DM17-IDC3D", col2: "ASR 9003", col3: "2U", rack: "W202", location: "IDC Lantai 2" },
                { hostname: "JKIDC-01-1-NAP-RT2", col2: "ASR 9903", col3: "2U", rack: "W202", location: "IDC Lantai 2" }
            ],
            DEVICEMMR3: [
                { hostname: "JK-IKT-41-1-SW5-IDC", col2: "ALU OS6860E-U28", col3: "1U", rack: "MMR 12", location: "IDC Lantai 3" },
                { hostname: "JK-IKT-41-SW1-IDC3D", col2: "S6730", col3: "1U", rack: "MMR 19", location: "IDC Lantai 3" },
                { hostname: "JK-IKT-41-1-SW3-IDC-DUREN3", col2: "OS6860E-24", col3: "1U", rack: "MMR 12", location: "IDC Lantai 3" },
                { hostname: "JKIDC-01-NAP-SW1", col2: "HUAWEI S6730", col3: "1U", rack: "MMR 19", location: "IDC Lantai 3" },
                { hostname: "JKIDC3D-01-06-MTR-PE56", col2: "ASR 920", col3: "1U", rack: "MMR 19", location: "IDC Lantai 3" },
                { hostname: "JK-IKT-41-SW2-IDC3D (Fiberstar 1 Baru)", col2: "HUAWEI S6730", col3: "1U", rack: "MMR 12", location: "IDC Lantai 3" },
                { hostname: "JKT-41-1-SW6-IDC-DUREN3", col2: "S6730", col3: "1U", rack: "MMR 31", location: "IDC Lantai 3" },
                { hostname: "JKT-41-1-SW7-IDC_DUREN3", col2: "S6730", col3: "1U", rack: "MMR 31", location: "IDC Lantai 3" },
                { hostname: "JKT-41-1-SW8-IDC_DUREN3", col2: "S6730", col3: "1U", rack: "MMR 29", location: "IDC Lantai 3" },
                { hostname: "JKIDC3D-01-10-MTR-PE86", col2: "ASR 920", col3: "1U", rack: "MMR 9", location: "IDC Lantai 3" }
            ],
            OTBMMR3: [
                { hostname: "OTB Lama FIBERSTAR 1 to Wi 165", col2: "-", col3: "24 Core", rack: "MMR 20", location: "IDC Lantai 3" },
                { hostname: "OTB Lama FIBERSTAR 1 to Wi 171", col2: "-", col3: "-", rack: "MMR 20", location: "IDC Lantai 3" },
                { hostname: "OTB Lama FIBERSTAR 2 to W202", col2: "SC", col3: "OTB 12 Core", rack: "MMR 32", location: "IDC Lantai 3" },
                { hostname: "OTB Lama FIBERSTAR 2 to W165", col2: "SC", col3: "OTB 12 Core", rack: "MMR 32", location: "IDC Lantai 3" },
                { hostname: "OTB NEW FIBERSTAR 3 to W202", col2: "LC", col3: "OTB 96 Core", rack: "MMR 41", location: "IDC Lantai 3" },
                { hostname: "OTB NEW FIBERSTAR 4 to W202", col2: "LC", col3: "OTB 96 Core", rack: "MMR 40", location: "IDC Lantai 3" },
                { hostname: "OTB NEW FIBERSTAR 4 to W165", col2: "LC", col3: "OTB 96 Core", rack: "MMR 40", location: "IDC Lantai 3" },
                { hostname: "OTB NEW FIBERSTAR 5 to W165", col2: "LC", col3: "OTB 96 Core", rack: "MMR 41", location: "IDC Lantai 3" }
            ],
            MMRNEW2: [
                { hostname: "JK-IKT-41-SW1-IDC3D Fiberstar-1(#7)", col2: "Huawei S6730", col3: "1U", rack: "Rack 1", location: "Gedung Baru Lantai 2 MMR" },
                { hostname: "JK-IKT 41 SW12 IDC3D Fiberstar-3(#7)", col2: "Huawei S6750", col3: "1U", rack: "Rack 2", location: "Gedung Baru Lantai 2 MMR" },
                { hostname: "JK-IKT 41-SW13 IDC3D Fiberstar-2(#7)", col2: "Huawei S6750", col3: "1U", rack: "Rack 4", location: "Gedung Baru Lantai 2 MMR" },
                { hostname: "JK-KYB-146-SW10-IDCD1 Fiberstar-0(#7)", col2: "Huawei S6750", col3: "1U", rack: "Rack 13", location: "Gedung Baru Lantai 2 MMR" }
            ],
            RACKDWDM2: [
                { hostname: "JKT-41-2-UAJB-F", col2: "DWDM", col3: "4RU", rack: "IDC3D#7 Rack dwdm 014", location: "Gd.Baru Lantai 2" },
                { hostname: "JKT-41-2-UAJB-F", col2: "DWDM", col3: "4RU", rack: "IDC3D#7 Rack dwdm 014", location: "Gd.Baru Lantai 2" }
            ],
            GBNEW3: [
                { hostname: "JK-KYB-140-SC1-IDC3", col2: "Huawei NE 8000 X4", col3: "8U", rack: "Rack SU7303", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-P1-IDC3", col2: "Huawei NE 8000E F2C", col3: "2U", rack: "Rack SU7303", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-SC2-IDC3", col2: "Huawei NE 8000 X4", col3: "8U", rack: "Rack SU7304", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-P2-IDC3", col2: "Huawei NE 8000E F2C", col3: "2U", rack: "Rack SU7304", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-DM8-IDC3D", col2: "HUAWEI NE8000 F2A", col3: "2U", rack: "Rack SU7306", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-134-P1-IDC3D", col2: "HUAWEI NE8000 F2A", col3: "2U", rack: "Rack SU7305", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-NAP-P1-IDCD3", col2: "Huawei NE 8000E F2C", col3: "2U", rack: "Rack SU7331", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-NAP-RT1-IDCD3", col2: "Huawei NE 8000 X4", col3: "8U", rack: "Rack SU7331", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-NAP-P2-IDCD3", col2: "Huawei NE 8000E F2C", col3: "2U", rack: "Rack SU7332", location: "Gd.Baru Lantai 3" },
                { hostname: "JK-KYB-140-NAP-RT2-IDCD3", col2: "Huawei NE 8000 X4", col3: "8U", rack: "Rack SU7332", location: "Gd.Baru Lantai 3" }
            ]
        },
        CSF: [
            { hostname: "JKCSFMMR1-01-03-MTR-PE87", col2: "ASR-920-24SZ", col3: "1U", rack: "L1.MMR01.02-02", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "JK-KYB-04-SW1-CSFCB1", col2: "Alcatel lucent 6860", col3: "1U", rack: "L1.MMR01.02-02", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "JKTCSF-MMR1-SW1-CSF", col2: "Alcatel lucent 6860 U28", col3: "1U", rack: "L1.MMR01.02-09", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "CSF-01-1-MTR-RT1", col2: "NCS 5501", col3: "2U", rack: "L1.MMR01.02-09", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "JK-KYB-04-PE1-CSFCB1", col2: "Huawei NE40E", col3: "1U", rack: "L1.MMR01.03-03", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "OTB Fiberstar MMR 1 48 Core", col2: "-", col3: "2U", rack: "L1.MMR01-ODF RACK F", location: "MMR 1 CSF CYBER 1 Lantai 1" },
            { hostname: "JKCSFMMR2-01-03-MTR-PE88", col2: "ASR-920-24SZ", col3: "1U", rack: "L1.MMR02.02-02", location: "MMR 2 CSF CYBER 1 Lantai 1" },
            { hostname: "OTB Fiberstar MMR 2 48 Core", col2: "-", col3: "2U", rack: "L1.MMR02.03-01", location: "-" },
            { hostname: "CSF-01-1-MTR-P1", col2: "NCS 55A1", col3: "2U", rack: "L3.MMR04.01-04", location: "MMR 4 CSF CYBER 1 Lantai 3" },
            { hostname: "JK-KYB-04-PE2-CSFCB1", col2: "NE8000 F2D", col3: "2U", rack: "L4.MMR05.01-03", location: "MMR 5 CSF CYBER 1 Lantai 4" },
            { hostname: "JK-KYB-04-NAP-RT1-CSFC1", col2: "NE8000 F2D", col3: "2U", rack: "L4.MMR05.01-03", location: "MMR 5 CSF CYBER 1 Lantai 4" },
            { hostname: "JK-KYB-04-NAP-RT2-CSFC1", col2: "NE8000 F2D", col3: "2U", rack: "L4.MMR05.01-03", location: "MMR 5 CSF CYBER 1 Lantai 4" },
            { hostname: "OTB 48 core", col2: "OTB LC-LC", col3: "1U", rack: "L4.MMR05.01-03", location: "MMR 5 CSF CYBER 1 Lantai 4" },
            { hostname: "RESONA-SW-ALU-6250-CSF-MMRLT2", col2: "Alcatel lucent 6250", col3: "1U", rack: "-", location: "Belum tau rack" },
            { hostname: "CYBCSF-SW-ALU-6250-PRISMALINK", col2: "Alcatel lucent 6250", col3: "1U", rack: "-", location: "Belum tau rack" },
            { hostname: "CYBCSF-SW-CSCO-2950-MMRLT1", col2: "Switch cisco 2950", col3: "1U", rack: "-", location: "MMR 1 CSF CYBER 1 Lantai 1" }
        ],
        MTEN: [
            { hostname: "JK-KYB-12-PE1-MTEN", col2: "NE8000 F1A", col3: "1U", rack: "Room DC2, CAGE DC2-C1-2 RACK 1-01", location: "MTEN NEX DATACENTER" },
            { hostname: "JK-KYB-12-PE2-MTEN", col2: "NE8000 F2A", col3: "2U", rack: "Room DC2, CAGE DC2-C1-2 RACK 1-01", location: "MTEN NEX DATACENTER" },
            { hostname: "JK-KYB-12-PE3-MTEN", col2: "NE8000 F2A", col3: "2U", rack: "Room DC2, CAGE DC2-C1-2 RACK 1-01", location: "MTEN NEX DATACENTER" },
            { hostname: "JK-MTEN-01-01-MTR-MMR", col2: "ASR920", col3: "1U", rack: "ISP Room, Rack MMR", location: "MTEN NEX DATACENTER" }
        ],
        GABUNGAN: {
            ALL: [
                { hostname: "JK-KYB-09-PE2-GEDGE", col2: "HUAWEI NE8000 F1A", col3: "1U", rack: "MMR", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-SW1-GEDGE", col2: "ALU 6860E-U28", col3: "1U", rack: "Lt.3_EG1-RCK-3-001-001__U-18", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-PE1-GEDGE", col2: "HUAWEI NE8000 F1A", col3: "1U", rack: "Half Rack1, U34", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-PE3-GEDGE", col2: "HUAWEI NE8000 F2D", col3: "2U", rack: "EG1-RCK-3-015-001", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-146-PE1-EDGE2", col2: "HUAWEI NE8000 M1D", col3: "1U", rack: "-", location: "GEDUNG EDGE 2" },
                { hostname: "JB-BGR-10-PE1-ELTR", col2: "ASR-920-24SZ-IM", col3: "1U", rack: "-", location: "DC ELITERY BOGOR" },
                { hostname: "JK-KYB-150-PE1-EQXJK", col2: "HUAWEI NE8000 M1D", col3: "1U", rack: "06:05A010:0106", location: "DC EQUINIX JKT" },
                { hostname: "JK-KYB-150-SW1-EQXJK", col2: "HUAWEI S5736", col3: "1U", rack: "06:05A010:0106", location: "DC EQUINIX JKT" },
                { hostname: "JB-DPK-07-PE1-A31DC", col2: "HUAWEI S6730", col3: "1U", rack: "MMR-A RACK A5", location: "DC Area 31 Bogor" },
                { hostname: "JB-CKR-03-PE1-DCCBT", col2: "Huawei F1A", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-PE3-DCCBT", col2: "Huawei F2D", col3: "2U", rack: "JK1A Rack 02", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-SW4-DCCBT", col2: "ALU X72", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-SW1-DCCBT", col2: "ALU X72", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" }
            ],
            EDGE: [
                { hostname: "JK-KYB-09-PE2-GEDGE", col2: "HUAWEI NE8000 F1A", col3: "1U", rack: "MMR", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-SW1-GEDGE", col2: "ALU 6860E-U28", col3: "1U", rack: "Lt.3_EG1-RCK-3-001-001__U-18", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-PE1-GEDGE", col2: "HUAWEI NE8000 F1A", col3: "1U", rack: "Half Rack1, U34", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-09-PE3-GEDGE", col2: "HUAWEI NE8000 F2D", col3: "2U", rack: "EG1-RCK-3-015-001", location: "DC EDGE TOWER" },
                { hostname: "JK-KYB-146-PE1-EDGE2", col2: "HUAWEI NE8000 M1D", col3: "1U", rack: "-", location: "GEDUNG EDGE 2" }
            ],
            ELITERY: [
                { hostname: "JB-BGR-10-PE1-ELTR", col2: "ASR-920-24SZ-IM", col3: "1U", rack: "-", location: "DC ELITERY BOGOR" }
            ],
            EQUINIX: [
                { hostname: "JK-KYB-150-PE1-EQXJK", col2: "HUAWEI NE8000 M1D", col3: "1U", rack: "06:05A010:0106", location: "DC EQUINIX JKT" },
                { hostname: "JK-KYB-150-SW1-EQXJK", col2: "HUAWEI S5736", col3: "1U", rack: "06:05A010:0106", location: "DC EQUINIX JKT" }
            ],
            AREA31: [
                { hostname: "JB-DPK-07-PE1-A31DC", col2: "HUAWEI S6730", col3: "1U", rack: "MMR-A RACK A5", location: "DC Area 31 Bogor" }
            ],
            DCICIBITUNG: [
                { hostname: "JB-CKR-03-PE1-DCCBT", col2: "Huawei F1A", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-PE3-DCCBT", col2: "Huawei F2D", col3: "2U", rack: "JK1A Rack 02", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-SW4-DCCBT", col2: "ALU X72", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" },
                { hostname: "JB-CKR-03-SW1-DCCBT", col2: "ALU X72", col3: "1U", rack: "JK1A Rack 01", location: "DC DCI CIBITUNG" }
            ]
        }
    };

    const STORAGE_KEY = 'dc_inventory_data_v1';

    function loadSavedData() {
        try {
            const saved = localStorage.getItem(STORAGE_KEY);
            if (saved) {
                const parsed = JSON.parse(saved);
                Object.keys(parsed).forEach(dcKey => {
                    if (Array.isArray(parsed[dcKey]) && Array.isArray(dataCenters[dcKey])) {
                        dataCenters[dcKey] = parsed[dcKey];
                    } else if (typeof parsed[dcKey] === 'object' && typeof dataCenters[dcKey] === 'object') {
                        Object.keys(parsed[dcKey]).forEach(subKey => {
                            if (Array.isArray(parsed[dcKey][subKey])) {
                                dataCenters[dcKey][subKey] = parsed[dcKey][subKey];
                            }
                        });
                    }
                });
            }
        } catch (e) {
            console.warn('Gagal memuat data tersimpan:', e);
        }
    }

    function saveAllData() {
        try {
            localStorage.setItem(STORAGE_KEY, JSON.stringify(dataCenters));
        } catch (e) {
            console.warn('Gagal menyimpan data ke browser:', e);
        }
    }

    function getAllCyber1Data() {
        return [
            ...dataCenters.CYBER1.APJII_OMNI,
            ...dataCenters.CYBER1.MMR_LT7,
            ...dataCenters.CYBER1.LT10_RACK_A,
            ...dataCenters.CYBER1.LT10_RACK_B,
            ...dataCenters.CYBER1.LT10_RACK_C,
            ...dataCenters.CYBER1.LT10_OTB_B09,
            ...dataCenters.CYBER1.LT10_WING_KIRI
        ];
    }

    function getAllGlobalData() {
        let allData = [];
        allData = allData.concat(dataCenters.NEXCYBER2);
        allData = allData.concat(getAllCyber1Data());
        allData = allData.concat(dataCenters.IDCDUREN3.ALL);
        allData = allData.concat(dataCenters.CSF);
        allData = allData.concat(dataCenters.MTEN);
        allData = allData.concat(dataCenters.GABUNGAN.ALL);
        return allData;
    }

    let activeDC = 'NEXCYBER2';
    let activeSubDC = 'ALL';

    function getTargetSubKey() {
        if (activeDC === 'CYBER1') {
            return (activeSubDC === 'ALL' || activeSubDC === 'LAYOUT_LT10') ? 'APJII_OMNI' : activeSubDC;
        }
        if (activeDC === 'IDCDUREN3') {
            return activeSubDC === 'ALL' ? 'W165' : activeSubDC;
        }
        if (activeDC === 'GABUNGAN') {
            return activeSubDC === 'ALL' ? 'EDGE' : activeSubDC;
        }
        return null;
    }

    function updateFormTargetLabel() {
        const labelEl = document.getElementById('targetCategoryLabel');
        if (!labelEl) return;
        const dcNames = {
            NEXCYBER2: 'NEX CYBER 2',
            CYBER1: 'CYBER 1',
            IDCDUREN3: 'IDC DUREN 3',
            CSF: 'CSF',
            MTEN: 'MTEN',
            GABUNGAN: 'Gabungan Beberapa DC'
        };
        const subKey = getTargetSubKey();
        if (!subKey) {
            labelEl.textContent = dcNames[activeDC] || activeDC;
        } else {
            labelEl.textContent = `${dcNames[activeDC] || activeDC} (${subKey})`;
        }
    }

    function toggleAddForm() {
        const formBox = document.getElementById('addFormContainer');
        const btn = document.getElementById('toggleAddBtn');
        const isShown = formBox.classList.toggle('show');
        btn.classList.toggle('active', isShown);
        if (isShown) {
            updateFormTargetLabel();
            document.getElementById('newHostname').focus();
        }
    }

    function handleAddKeyPress(event) {
        if (event.key === 'Enter') {
            event.preventDefault();
            submitNewDevice();
        }
    }

    function submitNewDevice() {
        const hostname = document.getElementById('newHostname').value.trim();
        const device = document.getElementById('newDevice').value.trim();
        const dimensi = document.getElementById('newDimensi').value.trim();
        const rack = document.getElementById('newRack').value.trim();
        const location = document.getElementById('newLocation').value.trim();

        if (!hostname && !device && !dimensi && !rack && !location) {
            document.getElementById('newHostname').focus();
            return;
        }

        const newItem = {
            hostname: hostname || '-',
            col2: device || '-',
            col3: dimensi || '-',
            rack: rack || '-',
            location: location || '-'
        };

        if (Array.isArray(dataCenters[activeDC])) {
            dataCenters[activeDC].unshift(newItem);
        } else {
            const targetSub = getTargetSubKey();
            if (targetSub && Array.isArray(dataCenters[activeDC][targetSub])) {
                dataCenters[activeDC][targetSub].unshift(newItem);
            }
            if (activeDC === 'IDCDUREN3' && Array.isArray(dataCenters.IDCDUREN3.ALL)) {
                dataCenters.IDCDUREN3.ALL.unshift(newItem);
            }
            if (activeDC === 'GABUNGAN' && Array.isArray(dataCenters.GABUNGAN.ALL)) {
                dataCenters.GABUNGAN.ALL.unshift(newItem);
            }
        }

        saveAllData();

        // Bersihkan semua kolom input
        document.getElementById('newHostname').value = '';
        document.getElementById('newDevice').value = '';
        document.getElementById('newDimensi').value = '';
        document.getElementById('newRack').value = '';
        document.getElementById('newLocation').value = '';

        // Jika sedang membuka Denah/Layout Lt 10, alihkan ke tabel agar hasil langsung terlihat
        if (activeDC === 'CYBER1' && activeSubDC === 'LAYOUT_LT10') {
            activeSubDC = 'APJII_OMNI';
            const subBtns = document.querySelectorAll('.sub-tab-btn');
            subBtns.forEach(b => b.classList.remove('active'));
            if (subBtns[1]) subBtns[1].classList.add('active');
        }

        // Sesuaikan hasil tabel dan jumlah perangkat secara otomatis
        filterTable();
        document.getElementById('newHostname').focus();
    }

    function updateActiveTitle() {
        let titles = {
            NEXCYBER2: 'NEX CYBER 2',
            CYBER1: 'CYBER 1 (Semua Area)',
            IDCDUREN3: 'IDC DUREN 3',
            CSF: 'CSF',
            MTEN: 'MTEN',
            GABUNGAN: 'Gabungan Beberapa DC'
        };
        let searchVal = document.getElementById('searchInput').value.trim();
        if (searchVal !== '') {
            document.getElementById('currentDcTitle').textContent = `Hasil Pencarian Global untuk: "${searchVal}"`;
            return;
        }

        if (activeDC === 'CYBER1' && activeSubDC !== 'ALL') {
            let cyber1SubTitles = {
                APJII_OMNI: 'Cyber 1 — APJII & Omni (Lt 1 - Lt 2)',
                MMR_LT7: 'Cyber 1 — MMR IDC (Lantai 7)',
                LT10_RACK_A: 'Cyber 1 Lantai 10 — Row A (A01 - A.08)',
                LT10_RACK_B: 'Cyber 1 Lantai 10 — Row B (B01 - B.08)',
                LT10_RACK_C: 'Cyber 1 Lantai 10 — Row C (C.01 - C.08)',
                LT10_OTB_B09: 'Cyber 1 Lantai 10 — OTB & T-Line (Rack B.09)',
                LT10_WING_KIRI: 'Cyber 1 Lantai 10 — Wing Kiri & DWDM',
                LAYOUT_LT10: 'Layout Cyb1 Lantai 10'
            };
            document.getElementById('currentDcTitle').textContent = cyber1SubTitles[activeSubDC] || titles.CYBER1;
        } else if (activeDC === 'GABUNGAN' && activeSubDC !== 'ALL') {
            let subTitles = {
                EDGE: 'Edge Data Center',
                ELITERY: 'Elitery Data Center',
                EQUINIX: 'Equinix Data Center',
                AREA31: 'Area 31 Data Center',
                DCICIBITUNG: 'DCI Cibitung'
            };
            document.getElementById('currentDcTitle').textContent = subTitles[activeSubDC] || titles.GABUNGAN;
        } else if (activeDC === 'IDCDUREN3' && activeSubDC !== 'ALL') {
            let idcSubTitles = {
                W165: 'Winter 165',
                TLINEW165: 'Tline Wi 165',
                W170: 'Winter 170',
                W171: 'Winter 171',
                OTBW171: 'OTB Wi 171',
                DWDMNOKIA: 'DWDM Nokia',
                W202L2: 'Winter 202 Lantai 2',
                DEVICEMMR3: 'Device MMR IDC Lantai 3',
                OTBMMR3: 'OTB MMR IDC Lantai 3',
                MMRNEW2: 'MMR IDC Lt 2 Gedung Baru',
                RACKDWDM2: 'Rack DWDM Lantai 2',
                GBNEW3: 'Gedung Baru Lt 3 IDC'
            };
            document.getElementById('currentDcTitle').textContent = idcSubTitles[activeSubDC] || titles.IDCDUREN3;
        } else {
            document.getElementById('currentDcTitle').textContent = titles[activeDC];
        }
    }

    function updateTableHeaders() {
        let headersTr = document.getElementById('tableHeaders');
        let searchVal = document.getElementById('searchInput').value.trim();
        if (searchVal !== '') {
            headersTr.innerHTML = `
                <th>Hostname</th>
                <th>Device</th>
                <th>Dimensi</th>
                <th>Rack</th>
                <th>Location</th>
            `;
            return;
        }
        if (
            (activeDC === 'IDCDUREN3' && (activeSubDC === 'TLINEW165' || activeSubDC === 'OTBW171' || activeSubDC === 'OTBMMR3')) ||
            (activeDC === 'CYBER1' && activeSubDC === 'LT10_OTB_B09')
        ) {
            headersTr.innerHTML = `
                <th>Hostname / Keterangan</th>
                <th>Tipe Port</th>
                <th>Jumlah Core</th>
                <th>Rack</th>
                <th>Location</th>
            `;
        } else {
            headersTr.innerHTML = `
                <th>Hostname</th>
                <th>Device</th>
                <th>Dimensi</th>
                <th>Rack</th>
                <th>Location</th>
            `;
        }
    }

    function toggleLayoutView(isLayout) {
        let layoutDiv = document.getElementById('layoutViewContainer');
        let tableDiv = document.getElementById('tableContainer');
        let badgeCount = document.getElementById('deviceCount');

        if (isLayout) {
            layoutDiv.style.display = 'block';
            tableDiv.style.display = 'none';
            badgeCount.textContent = 'Denah Lantai 10';
        } else {
            layoutDiv.style.display = 'none';
            tableDiv.style.display = 'block';
        }
    }

    function switchDC(dcKey, btnElement) {
        activeDC = dcKey;
        activeSubDC = 'ALL';
        document.getElementById('searchInput').value = '';
        toggleLayoutView(false);
        
        let buttons = document.querySelectorAll('.dc-tab-btn');
        buttons.forEach(btn => btn.classList.remove('active'));
        if(btnElement) {
            btnElement.classList.add('active');
        }

        let subTabsContainer = document.getElementById('subTabsContainer');
        subTabsContainer.innerHTML = '';

        if (dcKey === 'CYBER1') {
            subTabsContainer.classList.add('show');
            let menus = [
                { key: 'ALL', label: 'Semua Cyber 1' },
                { key: 'APJII_OMNI', label: 'APJII & Omni (Lt 1 - Lt 2)' },
                { key: 'MMR_LT7', label: 'MMR IDC (Lantai 7)' },
                { key: 'LT10_RACK_A', label: 'Lt 10 — Row A (A01 - A.08)' },
                { key: 'LT10_RACK_B', label: 'Lt 10 — Row B (B01 - B.08)' },
                { key: 'LT10_RACK_C', label: 'Lt 10 — Row C (C.01 - C.08)' },
                { key: 'LT10_OTB_B09', label: 'Lt 10 — OTB & T-Line (B.09)' },
                { key: 'LT10_WING_KIRI', label: 'Lt 10 — Wing Kiri & DWDM' },
                { key: 'LAYOUT_LT10', label: 'Layout Cyb1 Lantai 10' }
            ];
            menus.forEach((m, idx) => {
                let btn = document.createElement('button');
                btn.className = `sub-tab-btn ${idx === 0 ? 'active' : ''}`;
                btn.textContent = m.label;
                btn.onclick = (e) => switchSubDC(m.key, e.target);
                subTabsContainer.appendChild(btn);
            });
            renderTable(getAllCyber1Data());
        } else if (dcKey === 'GABUNGAN') {
            subTabsContainer.classList.add('show');
            let menus = [
                { key: 'ALL', label: 'Semua Gabungan' },
                { key: 'EDGE', label: 'Edge Data Center' },
                { key: 'ELITERY', label: 'Elitery Data Center' },
                { key: 'EQUINIX', label: 'Equinix Data Center' },
                { key: 'AREA31', label: 'Area 31 Data Center' },
                { key: 'DCICIBITUNG', label: 'DCI Cibitung' }
            ];
            menus.forEach((m, idx) => {
                let btn = document.createElement('button');
                btn.className = `sub-tab-btn ${idx === 0 ? 'active' : ''}`;
                btn.textContent = m.label;
                btn.onclick = (e) => switchSubDC(m.key, e.target);
                subTabsContainer.appendChild(btn);
            });
            renderTable(dataCenters.GABUNGAN.ALL);
        } else if (dcKey === 'IDCDUREN3') {
            subTabsContainer.classList.add('show');
            let menus = [
                { key: 'ALL', label: 'Semua IDC Duren 3' },
                { key: 'W165', label: 'Winter 165' },
                { key: 'TLINEW165', label: 'Tline Wi 165' },
                { key: 'W170', label: 'Winter 170' },
                { key: 'W171', label: 'Winter 171' },
                { key: 'OTBW171', label: 'OTB Wi 171' },
                { key: 'DWDMNOKIA', label: 'DWDM Nokia' },
                { key: 'W202L2', label: 'Winter 202 Lantai 2' },
                { key: 'DEVICEMMR3', label: 'Device MMR IDC Lt 3' },
                { key: 'OTBMMR3', label: 'OTB MMR IDC Lt 3' },
                { key: 'MMRNEW2', label: 'MMR IDC Lt 2 Gd. Baru' },
                { key: 'RACKDWDM2', label: 'Rack DWDM Lantai 2' },
                { key: 'GBNEW3', label: 'Gedung Baru Lt 3 IDC' }
            ];
            menus.forEach((m, idx) => {
                let btn = document.createElement('button');
                btn.className = `sub-tab-btn ${idx === 0 ? 'active' : ''}`;
                btn.textContent = m.label;
                btn.onclick = (e) => switchSubDC(m.key, e.target);
                subTabsContainer.appendChild(btn);
            });
            renderTable(dataCenters.IDCDUREN3.ALL);
        } else {
            subTabsContainer.classList.remove('show');
            renderTable(dataCenters[dcKey]);
        }
        updateActiveTitle();
        updateTableHeaders();
        updateFormTargetLabel();
    }

    function switchSubDC(subKey, btnElement) {
        activeSubDC = subKey;
        document.getElementById('searchInput').value = '';
        let buttons = document.querySelectorAll('.sub-tab-btn');
        buttons.forEach(btn => btn.classList.remove('active'));
        if(btnElement) {
            btnElement.classList.add('active');
        }

        if (activeDC === 'CYBER1' && subKey === 'LAYOUT_LT10') {
            toggleLayoutView(true);
            updateActiveTitle();
            updateFormTargetLabel();
            return;
        }

        toggleLayoutView(false);

        if (activeDC === 'CYBER1') {
            renderTable(subKey === 'ALL' ? getAllCyber1Data() : dataCenters.CYBER1[subKey]);
        } else if (activeDC === 'GABUNGAN') {
            renderTable(dataCenters.GABUNGAN[subKey]);
        } else if (activeDC === 'IDCDUREN3') {
            renderTable(dataCenters.IDCDUREN3[subKey]);
        }
        updateActiveTitle();
        updateTableHeaders();
        updateFormTargetLabel();
    }

    function renderTable(dataArray) {
        let tbody = document.getElementById('tableBody');
        tbody.innerHTML = '';

        if (!dataArray || dataArray.length === 0) {
            tbody.innerHTML = `<tr><td colspan="5" class="no-data">Tidak ada data perangkat ditemukan.</td></tr>`;
            document.getElementById('deviceCount').textContent = '0 perangkat';
            return;
        }

        document.getElementById('deviceCount').textContent = `${dataArray.length} perangkat`;

        dataArray.forEach(item => {
            let tr = document.createElement('tr');
            tr.innerHTML = `
                <td><strong>${item.hostname || '-'}</strong></td>
                <td>${item.col2 || '-'}</td>
                <td>${item.col3 || '-'}</td>
                <td><span class="rack-badge">${item.rack || '-'}</span></td>
                <td>${item.location || '-'}</td>
            `;
            tbody.appendChild(tr);
        });
    }

    function filterTable() {
        let input = document.getElementById('searchInput').value.toLowerCase().trim();
        let currentDataset = [];

        if (input === '') {
            if (activeDC === 'CYBER1' && activeSubDC === 'LAYOUT_LT10') {
                toggleLayoutView(true);
                updateActiveTitle();
                return;
            }
            toggleLayoutView(false);

            if (activeDC === 'CYBER1') {
                currentDataset = activeSubDC === 'ALL' ? getAllCyber1Data() : (dataCenters.CYBER1[activeSubDC] || []);
            } else if (activeDC === 'GABUNGAN') {
                currentDataset = dataCenters.GABUNGAN[activeSubDC] || [];
            } else if (activeDC === 'IDCDUREN3') {
                currentDataset = dataCenters.IDCDUREN3[activeSubDC] || [];
            } else {
                currentDataset = dataCenters[activeDC] || [];
            }
        } else {
            toggleLayoutView(false);
            currentDataset = getAllGlobalData();
        }

        let filtered = currentDataset.filter(item => {
            let host = (item.hostname || '').toLowerCase();
            let c2 = (item.col2 || '').toLowerCase();
            let c3 = (item.col3 || '').toLowerCase();
            let rack = (item.rack || '').toLowerCase();
            let loc = (item.location || '').toLowerCase();
            return host.includes(input) || c2.includes(input) || c3.includes(input) || rack.includes(input) || loc.includes(input);
        });

        renderTable(filtered);
        updateActiveTitle();
        updateTableHeaders();
    }

    // Load initial data (NEXCYBER2)
    window.onload = function() {
        loadSavedData();
        switchDC('NEXCYBER2', document.querySelector('.dc-tab-btn'));
    };
</script>

</body>
</html>
