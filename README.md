# Wersity-2
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=no">
    <meta name="theme-color" content="#2196F3">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <title>City Simulator 2 Advanced — прогрессивные улучшения</title>
    <style>
        * { margin:0; padding:0; box-sizing:border-box; -webkit-touch-callout:none; -webkit-user-select:none; user-select:none; }
        html,body { width:100%; height:100%; }
        body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif; background:#f0f4f8; overflow:hidden; }
        .app-container { width:100%; height:100%; display:flex; flex-direction:column; background:linear-gradient(180deg, #f0f4f8 0%, #e8f0f7 100%); }
        .header { background:linear-gradient(135deg,#2196F3 0%,#1976D2 100%); padding:12px 16px; text-align:center; box-shadow:0 4px 12px rgba(33,150,243,0.2); color:white; }
        .header h1 { font-size:20px; font-weight:700; }
        .header p { font-size:10px; opacity:0.9; margin-top:3px; }
        .main-container { flex:1; display:grid; grid-template-rows:1fr auto; gap:10px; padding:10px; overflow:hidden; }
        .game-section { background:white; border-radius:10px; overflow:hidden; box-shadow:0 4px 16px rgba(0,0,0,0.08); position:relative; }
        canvas { display:block; width:100%; height:100%; background:#2d5a3d; cursor:crosshair; touch-action:none; }
        .control-panel { background:white; border-radius:10px; padding:10px; box-shadow:0 4px 16px rgba(0,0,0,0.08); max-height:300px; overflow-y:auto; }
        .control-panel::-webkit-scrollbar { width:5px; }
        .control-panel::-webkit-scrollbar-thumb { background:#2196F3; border-radius:3px; }
        .status-bar { background:linear-gradient(135deg,#e3f2fd 0%,#f3e5f5 100%); padding:8px; border-radius:8px; display:flex; justify-content:space-between; align-items:center; font-size:11px; font-weight:600; margin-bottom:8px; }
        .status-dot { width:8px; height:8px; border-radius:50%; background:#ffc107; margin-right:4px; }
        .status-dot.active { background:#4caf50; }
        .section-header { font-size:11px; font-weight:700; color:#2196F3; text-transform:uppercase; letter-spacing:0.5px; padding:6px 0; border-bottom:2px solid #2196F3; margin-top:8px; margin-bottom:6px; }
        select { width:100%; padding:10px; border:2px solid #e0e0e0; border-radius:8px; background:white; color:#333; font-size:13px; font-weight:500; margin-bottom:8px; }
        select:active { border-color:#2196F3; }
        .building-card, .selected-card { background:linear-gradient(135deg,#e3f2fd 0%,#f3e5f5 100%); padding:8px; border-radius:8px; border-left:4px solid #2196F3; font-size:11px; margin-bottom:8px; }
        .building-card-title, .selected-card-title { font-weight:600; color:#1976D2; margin-bottom:4px; }
        .building-card-info, .selected-card-info { color:#424242; line-height:1.4; }
        .building-card-info span, .selected-card-info span { display:block; margin:2px 0; }
        .button-group { display:grid; grid-template-columns:1fr 1fr; gap:6px; margin-bottom:8px; }
        .button-group.full { grid-template-columns:1fr; }
        button { padding:10px; border:none; border-radius:8px; font-size:12px; font-weight:700; cursor:pointer; transition:all 0.2s; text-transform:uppercase; letter-spacing:0.3px; }
        .btn-play { background:linear-gradient(135deg,#4caf50 0%,#45a049 100%); color:white; grid-column:1 / -1; }
        .btn-play:active { transform:scale(0.96); }
        .btn-reset { background:linear-gradient(135deg,#ff9800 0%,#f57c00 100%); color:white; }
        .btn-reset:active { transform:scale(0.96); }
        .btn-delete { background:linear-gradient(135deg,#f44336 0%,#d32f2f 100%); color:white; }
        .btn-upgrade { background:linear-gradient(135deg,#ffa726 0%,#fb8c00 100%); color:white; }
        .stats-container { display:grid; grid-template-columns:1fr 1fr 1fr; gap:4px; }
        .stat-box { background:#f5f5f5; padding:6px; border-radius:6px; text-align:center; border-top:2px solid #2196F3; font-size:10px; }
        .stat-label { color:#999; font-weight:600; font-size:8px; text-transform:uppercase; margin-bottom:2px; }
        .stat-value { font-size:12px; font-weight:700; color:#1976D2; }
        .stat-value.positive { color:#4caf50; }
        .stat-value.negative { color:#f44336; }
        .stat-value.warning { color:#ff9800; }
        .hint-box { background:#c8e6c9; padding:6px; border-radius:6px; border-left:3px solid #4caf50; font-size:10px; color:#2e7d32; line-height:1.3; margin-top:8px; }
        .selected-card { display:none; }
    </style>
</head>
<body>
<div class="app-container">
    <div class="header">
        <h1>🏙️ City Simulator 2 Advanced</h1>
        <p>Улучшения дорожают с уровнем!</p>
    </div>
    <div class="main-container">
        <div class="game-section">
            <canvas id="gameCanvas"></canvas>
        </div>
        <div class="control-panel">
            <div class="status-bar">
                <div>
                    <span class="status-dot" id="statusDot"></span>
                    <span id="statusText">⏸️ Пауза</span>
                </div>
                <div>Зданий: <strong id="buildingCount" style="color:#2196F3;">0</strong></div>
            </div>
            <div class="section-header">🏗️ Выбор здания</div>
            <select id="buildingSelect">
                <option value="residential">🏠 Жилой дом - $5000</option>
                <option value="commercial">🏪 Магазин - $15000</option>
                <option value="industrial">🏭 Завод - $20000</option>
                <option value="hydroelectric">⚡ ГЭС - $30000</option>
                <option value="park">🌳 Парк - $500</option>
            </select>

            <div class="building-card" id="templateInfo">
                <div class="building-card-title" id="templateName">🏠 Жилой дом</div>
                <div class="building-card-info" id="templateDetails"></div>
            </div>

            <div class="selected-card" id="selectedInfo">
                <div class="selected-card-title" id="selectedName"></div>
                <div class="selected-card-info" id="selectedDetails"></div>
                <div class="button-group">
                    <button class="btn-delete" id="btnDelete">🗑️ Удалить</button>
                    <button class="btn-upgrade" id="btnUpgrade">⬆️ Улучшить</button>
                </div>
            </div>

            <div class="button-group full">
                <button class="btn-play" id="playBtn">▶️ Начать</button>
            </div>
            <div class="button-group">
                <button class="btn-reset" id="resetBtn">🔄 Новая</button>
            </div>

            <div class="section-header">📊 Статистика</div>
            <div class="stats-container">
                <div class="stat-box"><div class="stat-label">💰 Бюджет</div><div class="stat-value" id="budget">50K</div></div>
                <div class="stat-box"><div class="stat-label">👥 Население</div><div class="stat-value" id="population">0</div></div>
                <div class="stat-box"><div class="stat-label">⏱️ Время</div><div class="stat-value" id="time">0сек</div></div>
                <div class="stat-box"><div class="stat-label">💼 Занято</div><div class="stat-value" id="employed">0</div></div>
                <div class="stat-box"><div class="stat-label">😊 Счастье</div><div class="stat-value" id="happiness">50%</div></div>
                <div class="stat-box"><div class="stat-label">⚡ Энергия</div><div class="stat-value positive" id="energy">0</div></div>
            </div>
            <div class="hint-box">
                💡 Улучшения становятся дороже с ростом уровня! Парки спасают от уныния домов.
            </div>
        </div>
    </div>
</div>
<script>
    (function() {
        const MAX_LEVEL = 10;
        const BUILDINGS = {
            residential: {
                name: 'Жилой дом',
                baseCost: 5000,
                baseCapacity: 50,
                baseIncome: 0,
                baseRequired: 0,
                upgradeCostFactor: 1.25,
                incomeFactor: 0,
                requiredFactor: 0,
                capacityLevels: [50, 100, 150, 200, 300, 400, 500, 600, 750, 1000],
                getHappinessPenalty(level) {
                    return Math.pow(2, level - 1);
                }
            },
            commercial: {
                name: 'Магазин',
                baseCost: 15000,
                baseCapacity: 0,
                baseIncome: 600,
                baseRequired: 100,
                upgradeCostFactor: 1.5,
                incomeFactor: 0.5,
                requiredFactor: 0.75
            },
            industrial: {
                name: 'Завод',
                baseCost: 20000,
                baseCapacity: 0,
                baseIncome: 1000,
                baseRequired: 50,
                upgradeCostFactor: 1.5,
                incomeFactor: 0.5,
                requiredFactor: 0.75
            },
            hydroelectric: {
                name: 'ГЭС',
                baseCost: 30000,
                baseCapacity: 0,
                baseIncome: 2000,
                baseRequired: 500,
                upgradeCostFactor: 1.5,
                incomeFactor: 0.5,
                requiredFactor: 0.75
            },
            park: {
                name: 'Парк',
                baseCost: 500,
                baseCapacity: 0,
                baseIncome: 0,
                baseRequired: 0,
                upgradeCostFactor: 1.25,
                incomeFactor: 0,
                requiredFactor: 0,
                getCapacity(level) {
                    return Math.pow(2, level);
                },
                getHappinessBonus(level) {
                    return Math.pow(2, level - 1);
                }
            }
        };

        const CELL_SIZE = 30;
        const WATER_THRESHOLD = 0.62;
        const MIN_ZOOM = 0.5;
        const MAX_ZOOM = 2.0;

        let gameState = {
            budget: 50000,
            population: 0,
            employed: 0,
            happiness: 50,
            time: 0,
            buildings: [],
            running: false,
            income: 0,
            energy: 0,
            zoom: 1,
            cameraX: 0,
            cameraY: 0,
            happinessTimer: 0
        };

        let selectedBuildingType = 'residential';
        let selectedBuildingIndex = null;

        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        function resizeCanvas() {
            const rect = canvas.parentElement.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;
            draw();
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        function hash(x,y) { let h=Math.sin(x*12.9898+y*78.233)*43758.5453; return h-Math.floor(h); }
        function noise(x,y) {
            const xi=Math.floor(x), yi=Math.floor(y), xf=x-xi, yf=y-yi;
            const u=xf*xf*(3-2*xf), v=yf*yf*(3-2*yf);
            const n00=hash(xi,yi), n10=hash(xi+1,yi), n01=hash(xi,yi+1), n11=hash(xi+1,yi+1);
            const nx0=n00*(1-u)+n10*u, nx1=n01*(1-u)+n11*u;
            return nx0*(1-v)+nx1*v;
        }
        function isWater(gx,gy) { return noise(gx*0.1, gy*0.1) > WATER_THRESHOLD; }

        function getGridPosition(screenX, screenY) {
            const worldX = screenX / gameState.zoom + gameState.cameraX;
            const worldY = screenY / gameState.zoom + gameState.cameraY;
            return {
                x: Math.floor(worldX / CELL_SIZE),
                y: Math.floor(worldY / CELL_SIZE)
            };
        }

        function getBuildingLevel(building) { return building.level || 1; }

        function getBuildingCapacity(building) {
            const type = BUILDINGS[building.type];
            if (building.type === 'residential') {
                const idx = Math.min(getBuildingLevel(building), MAX_LEVEL) - 1;
                return type.capacityLevels[idx] || type.capacityLevels[type.capacityLevels.length-1];
            }
            return type.baseCapacity;
        }

        function getBuildingIncome(building) {
            const type = BUILDINGS[building.type];
            if (type.baseIncome === 0) return 0;
            return Math.round(type.baseIncome * (1 + (getBuildingLevel(building)-1) * type.incomeFactor));
        }

        function getBuildingRequired(building) {
            const type = BUILDINGS[building.type];
            return Math.round(type.baseRequired * (1 + (getBuildingLevel(building)-1) * type.requiredFactor));
        }

        // НОВАЯ ФОРМУЛА: стоимость улучшения растёт с уровнем
        function getUpgradeCost(building) {
            const type = BUILDINGS[building.type];
            const currentLevel = getBuildingLevel(building);
            return Math.round(type.baseCost * type.upgradeCostFactor * currentLevel);
        }

        function drawBuildingByLevel(type, level, size, cx, cy) {
            const w = size - 4, h = size - 4;
            ctx.save();
            ctx.translate(cx, cy);
            ctx.shadowColor = 'rgba(0,0,0,0.5)';
            ctx.shadowBlur = 4;
            ctx.shadowOffsetX = 2;
            ctx.shadowOffsetY = 2;
            ctx.fillStyle = '#7cb342';
            ctx.fillRect(-size/2+1, -size/2+1, size-2, size-2);

            if (type === 'residential') {
                if (level <= 2) {
                    ctx.fillStyle = '#a0522d'; ctx.fillRect(-w/2, -h/6, w, h*0.5);
                    ctx.fillStyle = '#8b4513'; ctx.beginPath(); ctx.moveTo(-w/2-2, -h/6); ctx.lineTo(0, -h/2); ctx.lineTo(w/2+2, -h/6); ctx.closePath(); ctx.fill();
                } else if (level <= 4) {
                    ctx.fillStyle = '#cd853f'; ctx.fillRect(-w/2, -h/4, w, h*0.6);
                    ctx.fillStyle = '#b22222'; ctx.beginPath(); ctx.moveTo(-w/2-2, -h/4); ctx.lineTo(0, -h/2); ctx.lineTo(w/2+2, -h/4); ctx.closePath(); ctx.fill();
                    ctx.fillStyle = '#fdf5e6'; ctx.fillRect(-w/4, -h/8, w/6, h/6); ctx.fillRect(w/4-w/6, -h/8, w/6, h/6);
                } else if (level <= 6) {
                    ctx.fillStyle = '#dcdcdc'; ctx.fillRect(-w/2, -h/3, w, h*0.8);
                    ctx.fillStyle = '#4682b4'; ctx.fillRect(-w/2+2, -h/3+2, w-4, h*0.8-4);
                    for (let i=0; i<4; i++) ctx.fillStyle = '#fff', ctx.fillRect(-w/4 + i*w/8, -h/4 + i*h/8, w/10, h/10);
                } else if (level <= 9) {
                    ctx.fillStyle = '#d3d3d3'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#1e90ff'; ctx.fillRect(-w/2+2, -h/2+2, w-4, h-4);
                    ctx.fillStyle = '#fff'; for (let i=0; i<6; i++) ctx.fillRect(-w/3 + i*4, -h/3 + i*3, 2, 3);
                } else {
                    ctx.fillStyle = '#c0c0c0'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#00bfff'; ctx.fillRect(-w/3, -h/2, w/1.5, h);
                    ctx.fillStyle = '#fff'; ctx.fillRect(-w/4, -h/2+2, w/8, h/4); ctx.fillRect(w/8, -h/2+2, w/8, h/4);
                }
            } else if (type === 'commercial') {
                if (level <= 3) {
                    ctx.fillStyle = '#deb887'; ctx.fillRect(-w/2, -h/3, w, h*0.7);
                    ctx.fillStyle = '#ffd700'; ctx.fillRect(-w/3, -h/2, w*0.66, h/5);
                } else if (level <= 6) {
                    ctx.fillStyle = '#f5f5dc'; ctx.fillRect(-w/2, -h/3, w, h*0.8);
                    ctx.fillStyle = '#ff6347'; ctx.fillRect(-w/2+2, -h/3+2, w-4, h*0.8-4);
                    ctx.fillStyle = '#fff'; ctx.font = 'bold 6px Arial'; ctx.fillText('MALL', 0, 2);
                } else if (level <= 9) {
                    ctx.fillStyle = '#d3d3d3'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#ff8c00'; ctx.fillRect(-w/2+2, -h/2+2, w-4, h-4);
                    ctx.fillStyle = '#000'; ctx.font = 'bold 6px Arial'; ctx.fillText('SHOP', 0, -2);
                } else {
                    ctx.fillStyle = '#c0c0c0'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#ff1493'; ctx.fillRect(-w/3, -h/2, w/1.5, h);
                    ctx.fillStyle = '#fff'; ctx.fillRect(-w/4, -h/3, w/8, h/4); ctx.fillRect(w/8, -h/3, w/8, h/4);
                }
            } else if (type === 'industrial') {
                if (level <= 3) {
                    ctx.fillStyle = '#808080'; ctx.fillRect(-w/2, -h/4, w, h*0.6);
                    ctx.fillStyle = '#ffeb3b'; ctx.fillRect(-w/3, -h/6, w/8, h/5);
                } else if (level <= 6) {
                    ctx.fillStyle = '#696969'; ctx.fillRect(-w/2, -h/3, w, h*0.7);
                    ctx.fillStyle = '#ffa500'; ctx.fillRect(-w/4, -h/2, w/6, h/5);
                    ctx.fillStyle = '#bdbdbd'; ctx.beginPath(); ctx.arc(-w/6, -h/2-4, 3, 0, Math.PI*2); ctx.fill();
                } else if (level <= 9) {
                    ctx.fillStyle = '#2f4f4f'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#ff4500'; ctx.fillRect(-w/3, -h/2, w/4, h/5);
                    for (let i=0; i<3; i++) ctx.fillStyle = '#ffd700', ctx.fillRect(-w/3 + i*w/6, -h/3, w/8, h/6);
                } else {
                    ctx.fillStyle = '#708090'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#00ffff'; ctx.fillRect(-w/3, -h/2, w/1.5, h/4);
                    ctx.fillStyle = '#fff'; ctx.fillRect(-w/4, -h/3, w/8, h/6); ctx.fillRect(w/8, -h/3, w/8, h/6);
                }
            } else if (type === 'hydroelectric') {
                if (level <= 3) {
                    ctx.fillStyle = '#8b4513'; ctx.fillRect(-w/4, -h/3, w/2, h*0.6);
                    ctx.fillStyle = '#d2b48c'; ctx.beginPath(); ctx.arc(0, -h/4, w/5, 0, Math.PI*2); ctx.fill();
                } else if (level <= 6) {
                    ctx.fillStyle = '#a9a9a9'; ctx.fillRect(-w/2, -h/4, w, h*0.5);
                    ctx.fillStyle = '#4682b4'; ctx.fillRect(-w/3, -h/3, w/6, h*0.7);
                } else if (level <= 9) {
                    ctx.fillStyle = '#808080'; ctx.fillRect(-w/2, -h/3, w, h*0.6);
                    ctx.fillStyle = '#1e90ff'; ctx.fillRect(-w/3, -h/2, w/6, h*0.8);
                    ctx.fillStyle = '#ffd700'; ctx.beginPath(); ctx.arc(0, h/4, w/6, 0, Math.PI*2); ctx.fill();
                } else {
                    ctx.fillStyle = '#2f4f4f'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#00ffff'; ctx.fillRect(-w/3, -h/2, w/1.5, h/5);
                    ctx.fillStyle = '#fff'; ctx.beginPath(); ctx.arc(0, -h/4, w/5, 0, Math.PI*2); ctx.fill();
                }
            } else if (type === 'park') {
                if (level <= 2) {
                    ctx.fillStyle = '#228b22'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#006400'; ctx.beginPath(); ctx.arc(-w/5, -h/5, w/5, 0, Math.PI*2); ctx.fill();
                    ctx.fillStyle = '#8b4513'; ctx.fillRect(-1, -h/8, 2, h/4);
                    ctx.fillStyle = '#32cd32'; ctx.beginPath(); ctx.arc(0, -h/3, w/4, 0, Math.PI*2); ctx.fill();
                } else if (level <= 5) {
                    ctx.fillStyle = '#2e8b57'; ctx.fillRect(-w/2, -h/2, w, h);
                    ctx.fillStyle = '#006400'; ctx.beginPath(); ctx.arc(-w/4, -h/4, w/5, 0, Math.PI*2); ctx.fill();
                    ctx.beginPath(); ctx.arc(w/4, -h/4, w/5, 0, Math.PI*2); ctx.fill();
                    ctx.fillStyle = '#ffd700'; ctx.beginPath(); ctx.arc(0, -h/3, w/6, 0, Math.PI*2); ctx.fill();
                } else if (level <= 8) {
                    ctx.fillStyle = '#3cb371'; ctx.fillRect(-w/2, -h/2, w, h);
                    for (let i=0; i<3; i++) {
                        ctx.fillStyle = '#006400'; ctx.beginPath(); ctx.arc(-w/3 + i*w/3, -h/3, w/7, 0, Math.PI*2); ctx.fill();
                    }
                    ctx.fillStyle = '#ff69b4'; ctx.beginPath(); ctx.arc(0, -h/4, w/8, 0, Math.PI*2); ctx.fill();
                } else {
                    ctx.fillStyle = '#66cdaa'; ctx.fillRect(-w/2, -h/2, w, h);
                    for (let i=0; i<4; i++) {
                        ctx.fillStyle = '#004d00'; ctx.beginPath(); ctx.arc(-w/3 + i*w/4, -h/3, w/8, 0, Math.PI*2); ctx.fill();
                    }
                    ctx.fillStyle = '#ff1493'; ctx.beginPath(); ctx.arc(0, -h/3, w/6, 0, Math.PI*2); ctx.fill();
                    ctx.fillStyle = '#ffff00'; ctx.beginPath(); ctx.arc(w/4, -h/2, w/10, 0, Math.PI*2); ctx.fill();
                }
            }

            ctx.shadowColor = 'transparent';
            ctx.fillStyle = 'white';
            ctx.font = 'bold 10px Arial';
            ctx.textAlign = 'right';
            ctx.textBaseline = 'bottom';
            ctx.fillText(level, w/2-2, h/2-2);
            ctx.restore();
        }

        function draw() {
            ctx.fillStyle = '#3d7a4a';
            ctx.fillRect(0,0,canvas.width,canvas.height);
            const zoom = gameState.zoom, camX = gameState.cameraX, camY = gameState.cameraY;
            const startGX = Math.floor(camX/CELL_SIZE)-1, startGY = Math.floor(camY/CELL_SIZE)-1;
            const endGX = Math.ceil((camX + canvas.width/zoom)/CELL_SIZE)+1;
            const endGY = Math.ceil((camY + canvas.height/zoom)/CELL_SIZE)+1;
            ctx.strokeStyle = 'rgba(255,255,255,0.15)';
            ctx.lineWidth = 1;

            for (let gx=startGX; gx<=endGX; gx++) {
                for (let gy=startGY; gy<=endGY; gy++) {
                    const sx = (gx*CELL_SIZE - camX)*zoom, sy = (gy*CELL_SIZE - camY)*zoom, sz = CELL_SIZE*zoom;
                    if (isWater(gx,gy)) {
                        ctx.fillStyle = '#1e5a8e'; ctx.fillRect(sx,sy,sz,sz);
                        ctx.fillStyle = 'rgba(255,255,255,0.08)';
                        ctx.fillRect(sx+4*zoom, sy+4*zoom, sz-8*zoom, 2*zoom);
                    }
                    ctx.strokeRect(sx,sy,sz,sz);
                }
            }

            gameState.buildings.forEach((b, idx) => {
                const sx = (b.x*CELL_SIZE - camX)*zoom, sy = (b.y*CELL_SIZE - camY)*zoom, sz = CELL_SIZE*zoom;
                if (sx > -sz && sx < canvas.width && sy > -sz && sy < canvas.height) {
                    drawBuildingByLevel(b.type, getBuildingLevel(b), sz, sx+sz/2, sy+sz/2);
                    if (idx === selectedBuildingIndex) {
                        ctx.strokeStyle = '#FFD700'; ctx.lineWidth = 2; ctx.strokeRect(sx, sy, sz, sz);
                    }
                }
            });
        }

        function applyHappinessTick() {
            const houses = gameState.buildings.filter(b => b.type === 'residential');
            const parks = gameState.buildings.filter(b => b.type === 'park');
            houses.sort((a,b) => BUILDINGS.residential.getHappinessPenalty(getBuildingLevel(b)) - BUILDINGS.residential.getHappinessPenalty(getBuildingLevel(a)));
            parks.sort((a,b) => BUILDINGS.park.getCapacity(getBuildingLevel(b)) - BUILDINGS.park.getCapacity(getBuildingLevel(a)));

            let totalBonus = 0;
            let totalPenalty = 0;
            let remainingHouses = [...houses];

            for (const park of parks) {
                const capacity = BUILDINGS.park.getCapacity(getBuildingLevel(park));
                const taken = remainingHouses.splice(0, capacity);
                totalBonus += BUILDINGS.park.getHappinessBonus(getBuildingLevel(park));
            }

            for (const house of remainingHouses) {
                totalPenalty += BUILDINGS.residential.getHappinessPenalty(getBuildingLevel(house));
            }

            const delta = totalBonus - totalPenalty;
            gameState.happiness = Math.max(0, Math.min(100, gameState.happiness + delta));
        }

        function updateUI() {
            document.getElementById('budget').textContent = '$'+(gameState.budget/1000).toFixed(1)+'K';
            document.getElementById('population').textContent = gameState.population;
            document.getElementById('employed').textContent = gameState.employed;
            document.getElementById('happiness').textContent = gameState.happiness+'%';
            document.getElementById('time').textContent = gameState.time+'сек';
            document.getElementById('energy').textContent = gameState.energy;
            document.getElementById('buildingCount').textContent = gameState.buildings.length;
            const dot = document.getElementById('statusDot');
            if (gameState.running) { dot.classList.add('active'); document.getElementById('statusText').textContent='▶️ Активна'; }
            else { dot.classList.remove('active'); document.getElementById('statusText').textContent='⏸️ Пауза'; }
            updateSelectedInfo();
            draw();
        }

        function updateTemplateInfo() {
            const t = BUILDINGS[selectedBuildingType];
            document.getElementById('templateName').textContent = t.name;
            let details = `<span><strong>Цена постройки:</strong> $${t.baseCost}</span>`;
            if (t.baseCapacity) details += `<span><strong>Вместимость:</strong> ${t.baseCapacity}</span>`;
            else if (selectedBuildingType === 'park') details += `<span><strong>Обслуживает домов:</strong> ${BUILDINGS.park.getCapacity(1)}</span>`;
            details += `<span><strong>Доход:</strong> $${t.baseIncome}</span>`;
            if (t.baseRequired) details += `<span><strong>Требует рабочих:</strong> ${t.baseRequired}</span>`;
            if (selectedBuildingType === 'park') details += `<span><strong>Бонус счастья (1 ур):</strong> +${BUILDINGS.park.getHappinessBonus(1)}%</span>`;
            document.getElementById('templateDetails').innerHTML = details;
        }

        function updateSelectedInfo() {
            const selDiv = document.getElementById('selectedInfo');
            if (selectedBuildingIndex !== null && selectedBuildingIndex < gameState.buildings.length) {
                const b = gameState.buildings[selectedBuildingIndex];
                const type = BUILDINGS[b.type];
                const level = getBuildingLevel(b);
                const upgradeCost = level < MAX_LEVEL ? getUpgradeCost(b) : null;

                document.getElementById('selectedName').textContent = `${type.name} (ур. ${level})`;
                let details = '';
                if (b.type === 'residential') {
                    const capacity = getBuildingCapacity(b);
                    const penalty = BUILDINGS.residential.getHappinessPenalty(level);
                    details = `<span><strong>Вместимость:</strong> ${capacity}</span><span><strong>Штраф счастья:</strong> -${penalty}% в 10с</span>`;
                } else if (b.type === 'park') {
                    const capacity = BUILDINGS.park.getCapacity(level);
                    const bonus = BUILDINGS.park.getHappinessBonus(level);
                    details = `<span><strong>Обслуживает домов:</strong> ${capacity}</span><span><strong>Бонус счастья:</strong> +${bonus}%</span>`;
                } else {
                    const income = getBuildingIncome(b);
                    const required = getBuildingRequired(b);
                    details = `<span><strong>Доход:</strong> $${income}</span><span><strong>Нужно рабочих:</strong> ${required}</span>`;
                }
                details += `<span><strong>Позиция:</strong> ${b.x}, ${b.y}</span>`;
                if (upgradeCost) details += `<span><strong>Улучшение:</strong> $${upgradeCost}</span>`;
                document.getElementById('selectedDetails').innerHTML = details;
                document.getElementById('btnUpgrade').style.display = level < MAX_LEVEL ? 'block' : 'none';
                selDiv.style.display = 'block';
            } else {
                selDiv.style.display = 'none';
                selectedBuildingIndex = null;
            }
        }

        canvas.addEventListener('click', e => {
            const rect = canvas.getBoundingClientRect();
            const pos = getGridPosition(e.clientX-rect.left, e.clientY-rect.top);
            const clickedIndex = gameState.buildings.findIndex(b => b.x===pos.x && b.y===pos.y);
            if (clickedIndex !== -1) { selectedBuildingIndex = clickedIndex; updateUI(); return; }

            const building = BUILDINGS[selectedBuildingType];
            const water = isWater(pos.x, pos.y);
            if (building.baseCost > gameState.budget) return;
            if (building === BUILDINGS.hydroelectric && !water) return;
            if (building !== BUILDINGS.hydroelectric && water) return;

            gameState.budget -= building.baseCost;
            gameState.buildings.push({ x:pos.x, y:pos.y, type:selectedBuildingType, level:1 });
            if (selectedBuildingType === 'hydroelectric') gameState.energy += 200;
            selectedBuildingIndex = null;
            updateUI();
        });

        document.getElementById('buildingSelect').addEventListener('change', e => {
            selectedBuildingType = e.target.value; selectedBuildingIndex = null; updateTemplateInfo(); updateUI();
        });
        document.getElementById('playBtn').addEventListener('click', ()=>{ gameState.running = !gameState.running; updateUI(); });
        document.getElementById('resetBtn').addEventListener('click', ()=>{
            gameState = { budget:50000, population:0, employed:0, happiness:50, time:0, buildings:[], running:false, income:0, energy:0, zoom:1, cameraX:0, cameraY:0, happinessTimer:0 };
            selectedBuildingIndex = null; updateTemplateInfo(); updateUI();
        });
        document.getElementById('btnDelete').addEventListener('click', ()=>{
            if (selectedBuildingIndex === null) return;
            const b = gameState.buildings[selectedBuildingIndex];
            gameState.budget += Math.round(BUILDINGS[b.type].baseCost * 0.5);
            if (b.type === 'hydroelectric') gameState.energy -= 200;
            gameState.buildings.splice(selectedBuildingIndex, 1);
            selectedBuildingIndex = null;
            updateUI();
        });
        document.getElementById('btnUpgrade').addEventListener('click', ()=>{
            if (selectedBuildingIndex === null) return;
            const b = gameState.buildings[selectedBuildingIndex];
            const level = getBuildingLevel(b);
            if (level >= MAX_LEVEL) return;
            const cost = getUpgradeCost(b);
            if (gameState.budget < cost) return;
            gameState.budget -= cost;
            b.level = level + 1;
            updateUI();
        });

        canvas.addEventListener('wheel', e => {
            e.preventDefault();
            const delta = e.deltaY > 0 ? -0.1 : 0.1;
            gameState.zoom = Math.min(MAX_ZOOM, Math.max(MIN_ZOOM, gameState.zoom + delta));
            draw();
        });

        let touchStartX, touchStartY, pinchStartDist;
        canvas.addEventListener('touchstart', e => {
            if (e.touches.length === 1) { touchStartX = e.touches[0].clientX; touchStartY = e.touches[0].clientY; }
            else if (e.touches.length === 2) pinchStartDist = Math.hypot(e.touches[0].clientX - e.touches[1].clientX, e.touches[0].clientY - e.touches[1].clientY);
        });
        canvas.addEventListener('touchmove', e => {
            e.preventDefault();
            if (e.touches.length === 1 && touchStartX !== undefined) {
                const dx = e.touches[0].clientX - touchStartX, dy = e.touches[0].clientY - touchStartY;
                gameState.cameraX -= dx / gameState.zoom; gameState.cameraY -= dy / gameState.zoom;
                touchStartX = e.touches[0].clientX; touchStartY = e.touches[0].clientY;
                draw();
            } else if (e.touches.length === 2 && pinchStartDist) {
                const newDist = Math.hypot(e.touches[0].clientX - e.touches[1].clientX, e.touches[0].clientY - e.touches[1].clientY);
                gameState.zoom = Math.min(MAX_ZOOM, Math.max(MIN_ZOOM, gameState.zoom * (newDist / pinchStartDist)));
                pinchStartDist = newDist;
                draw();
            }
        }, { passive: false });
        canvas.addEventListener('touchend', ()=>{ touchStartX = undefined; pinchStartDist = undefined; });

        setInterval(() => {
            if (!gameState.running || gameState.buildings.length === 0) return;

            gameState.happinessTimer++;
            if (gameState.happinessTimer >= 10) {
                gameState.happinessTimer = 0;
                applyHappinessTick();
            }

            let totalCapacity = 0;
            gameState.buildings.forEach(b => { if (b.type === 'residential') totalCapacity += getBuildingCapacity(b); });

            let totalIncome = 0, totalEmployed = 0;
            const pop = gameState.population;
            gameState.buildings.forEach(b => {
                if (b.type === 'residential' || b.type === 'park') return;
                const required = getBuildingRequired(b);
                if (pop >= required) {
                    totalIncome += getBuildingIncome(b);
                    totalEmployed += required;
                }
            });
            totalEmployed = Math.min(totalEmployed, pop);

            const incomeMultiplier = gameState.happiness / 100;
            gameState.budget += Math.round(totalIncome * incomeMultiplier);

            gameState.population = Math.min(pop + 5, totalCapacity);
            gameState.employed = totalEmployed;
            gameState.time++;
            updateUI();
        }, 1000);

        updateTemplateInfo();
        updateUI();
    })();
</script>
</body>
</html>
