# Mtg-build
Constructions the decks in magic gathering
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MTG Deck Builder - Estándar & Commander</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background: linear-gradient(135deg, #1a1a2e, #16213e); color: #fff; min-height: 100vh; }
        
        .container { max-width: 1400px; margin: 0 auto; padding: 20px; display: grid; grid-template-columns: 1fr 400px; gap: 20px; height: 100vh; }
        
        /* Header */
        header { grid-column: 1 / -1; background: rgba(0,0,0,0.8); padding: 20px; border-radius: 15px; text-align: center; margin-bottom: 20px; }
        header h1 { font-size: 2.5rem; background: linear-gradient(45deg, #ffd700, #ff6b35); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        
        /* Search Panel */
        .search-panel { background: rgba(255,255,255,0.1); backdrop-filter: blur(10px); border-radius: 15px; padding: 20px; height: fit-content; }
        .search-controls { display: flex; gap: 10px; margin-bottom: 20px; flex-wrap: wrap; }
        select, input { padding: 10px; border: none; border-radius: 8px; background: rgba(255,255,255,0.9); color: #333; font-size: 14px; }
        .format-tabs { display: flex; gap: 10px; margin-bottom: 15px; }
        .format-tab { padding: 10px 20px; background: rgba(255,255,255,0.2); border-radius: 25px; cursor: pointer; transition: all 0.3s; }
        .format-tab.active { background: #ffd700; color: #333; }
        #searchBtn { background: linear-gradient(45deg, #ffd700, #ff6b35); color: #333; font-weight: bold; cursor: pointer; border: none; padding: 12px 24px; border-radius: 25px; }
        
        /* Cards Grid */
        .cards-grid { background: rgba(255,255,255,0.05); border-radius: 15px; padding: 20px; overflow-y: auto; max-height: 70vh; }
        .cards-grid h3 { margin-bottom: 15px; color: #ffd700; }
        .card-item { display: flex; align-items: center; padding: 12px; margin-bottom: 10px; background: rgba(255,255,255,0.1); border-radius: 10px; cursor: pointer; transition: all 0.3s; }
        .card-item:hover { background: rgba(255,215,0,0.3); transform: translateX(5px); }
        .card-image { width: 60px; height: 84px; object-fit: cover; border-radius: 5px; margin-right: 15px; }
        .card-info h4 { color: #ffd700; margin-bottom: 5px; }
        .card-mana { font-size: 18px; font-weight: bold; margin-right: 10px; }
        .card-type { color: #ccc; font-size: 12px; }
        
        /* Deck Panel */
        .deck-panel { background: rgba(255,255,255,0.1); backdrop-filter: blur(10px); border-radius: 15px; padding: 20px; display: flex; flex-direction: column; height: fit-content; }
        .deck-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .deck-stats { background: rgba(0,0,0,0.5); padding: 15px; border-radius: 10px; text-align: center; }
        .stat-row { display: flex; justify-content: space-around; margin-bottom: 10px; }
        .stat { font-size: 18px; font-weight: bold; }
        .deck-list { flex: 1; overflow-y: auto; max-height: 400px; margin-bottom: 20px; }
        .deck-card { display: flex; justify-content: space-between; align-items: center; padding: 8px; margin-bottom: 5px; background: rgba(255,255,255,0.1); border-radius: 5px; }
        .deck-card button { background: #ff4444; color: white; border: none; padding: 5px 10px; border-radius: 3px; cursor: pointer; }
        .export-section { background: rgba(0,0,0,0.5); padding: 15px; border-radius: 10px; }
        #exportBtn { width: 100%; background: linear-gradient(45deg, #28a745, #20c997); color: white; border: none; padding: 12px; border-radius: 8px; font-size: 16px; cursor: pointer; margin-bottom: 10px; }
        
        @media (max-width: 768px) { .container { grid-template-columns: 1fr; grid-template-rows: auto 1fr; height: auto; } }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🃏 MTG Deck Builder</h1>
            <p>Construye mazos Estándar o Commander con búsqueda Moxfield-style</p>
        </header>

        <!-- Search Panel -->
        <div class="search-panel">
            <div class="format-tabs">
                <div class="format-tab active" data-format="standard">🏆 Estándar</div>
                <div class="format-tab" data-format="commander">👑 Commander</div>
            </div>
            
            <div class="search-controls">
                <input type="text" id="searchInput" placeholder="Buscar cartas por nombre..." style="flex: 1;">
                <select id="colorFilter">
                    <option value="">Todas las Colores</option>
                    <option value="W">Blanco ⚪</option>
                    <option value="U">Azul 🔵</option>
                    <option value="B">Negro ⚫</option>
                    <option value="R">Rojo 🔴</option>
                    <option value="G">Verde 🟢</option>
                </select>
                <select id="typeFilter">
                    <option value="">Todos los Tipos</option>
                    <option value="creature">Criatura</option>
                    <option value="instant">Instantáneo</option>
                    <option value="sorcery">Conjuro</option>
                    <option value="enchantment">Encantamiento</option>
                    <option value="artifact">Artefacto</option>
                    <option value="land">Tierra</option>
                </select>
                <button id="searchBtn">🔍 Buscar</button>
            </div>
            
            <div class="cards-grid" id="cardsGrid">
                <h3>Resultados de búsqueda...</h3>
            </div>
        </div>

        <!-- Deck Panel -->
        <div class="deck-panel">
            <div class="deck-header">
                <h3 id="deckTitle">Mi Mazo</h3>
                <span id="totalCards">0 cartas</span>
            </div>
            
            <div class="deck-stats">
                <div class="stat-row">
                    <div class="stat" id="lands">0 Tierras</div>
                    <div class="stat" id="creatures">0 Criaturas</div>
                </div>
                <div class="stat-row">
                    <div class="stat" id="manaCurve">CMC: --</div>
                    <div class="stat" id="avgCMC">--</div>
                </div>
            </div>
            
            <div class="deck-list" id="deckList"></div>
            
            <div class="export-section">
                <button id="exportBtn">📤 Exportar Mazo (Texto)</button>
                <button id="clearDeckBtn" style="width:100%; background:#6c757d; color:white; border:none; padding:10px; border-radius:8px; cursor:pointer; margin-top:10px;">🗑️ Limpiar Mazo</button>
            </div>
        </div>
    </div>

    <script>
        class MTGDeckBuilder {
            constructor() {
                this.currentFormat = 'standard';
                this.deck = {};
                this.cards = [];
                this.init();
            }

            init() {
                this.bindEvents();
                this.searchCards(); // Carga cartas populares al inicio
            }

            bindEvents() {
                document.querySelectorAll('.format-tab').forEach(tab => {
                    tab.addEventListener('click', (e) => this.setFormat(e.target.dataset.format));
                });
                
                document.getElementById('searchBtn').addEventListener('click', () => this.searchCards());
                document.getElementById('searchInput').addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') this.searchCards();
                });
                
                document.getElementById('exportBtn').addEventListener('click', () => this.exportDeck());
                document.getElementById('clearDeckBtn').addEventListener('click', () => this.clearDeck());
            }

            setFormat(format) {
                this.currentFormat = format;
                document.querySelectorAll('.format-tab').forEach(tab => tab.classList.remove('active'));
                event.target.classList.add('active');
                document.getElementById('deckTitle').textContent = format === 'commander' ? 'Mi Commander' : 'Mi Estándar';
                this.searchCards();
            }

            async searchCards() {
                const query = document.getElementById('searchInput').value;
                const color = document.getElementById('colorFilter').value;
                const type = document.getElementById('typeFilter').value;
                
                let url = `https://api.scryfall.com/cards/search?q=`;
                
                if (query) url += `+name:${encodeURIComponent(query)}`;
                if (color) url += `+color:${color}`;
                if (type) url += `+type:${type}`;
                
                if (this.currentFormat === 'commander') {
                    url += `+is:commander-legal`;
                } else {
                    url += `+legal:standard`;
                }
                
                try {
                    const response = await fetch(url);
                    const data = await response.json();
                    this.cards = data.data;
                    this.renderCards();
                } catch (error) {
                    console.error('Error searching cards:', error);
                }
            }

            renderCards() {
                const grid = document.getElementById('cardsGrid');
                grid.innerHTML = `<h3>${this.cards.length} cartas encontradas</h3>`;
                
                this.cards.slice(0, 50).forEach(card => { // Limit to 50 for performance
                    const cardEl = document.createElement('div');
                    cardEl.className = 'card-item';
                    cardEl.innerHTML = `
                        <img src="${card.image_uris?.normal || card.card_faces?.[0]?.image_uris?.normal}" 
                             alt="${card.name}" class="card-image" onerror="this.src='https://via.placeholder.com/60x84/333/fff?text=?';">
                        <div>
                            <div class="card-mana">${card.mana_cost || '—'}</div>
                            <h4>${card.name}</h4>
                            <div class="card-type">${card.type_line}</div>
                        </div>
                    `;
                    cardEl.addEventListener('click', () => this.addToDeck(card));
                    grid.appendChild(cardEl);
                });
            }

            addToDeck(card) {
                const id = card.id;
                if (!this.deck[id]) {
                    this.deck[id] = { ...card, count: 0 };
                }
                
                if (this.currentFormat === 'commander') {
                    this.deck[id].count = Math.min(this.deck[id].count + 1, 1); // Commander: máximo 1
                } else {
                    this.deck[id].count = Math.min(this.deck[id].count + 1, 4); // Estándar: máximo 4
                }
                
                this.renderDeck();
            }

            removeFromDeck(cardId) {
                if (this.deck[cardId]) {
                    this.deck[cardId].count--;
                    if (this.deck[cardId].count <= 0) {
                        delete this.deck[cardId];
                    }
                    this.renderDeck();
                }
            }

            renderDeck() {
                const deckList = document.getElementById('deckList');
                const totalCards = Object.values(this.deck).reduce((sum, card) => sum + card.count, 0);
                
                deckList.innerHTML = '';
                
                Object.entries(this.deck).forEach(([id, card]) => {
                    if (card.count > 0) {
                        const cardEl = document.createElement('div');
                        cardEl.className = 'deck-card';
                        cardEl.innerHTML = `
                            <span>${card.count}x ${card.name}</span>
                            <button onclick="deckBuilder.removeFromDeck('${id}')">-</button>
                        `;
                        deckList.appendChild(cardEl);
                    }
                });
                
                document.getElementById('totalCards').textContent = `${totalCards} cartas`;
                this.updateStats();
            }

            updateStats() {
                let lands = 0, creatures = 0, totalCMC = 0, totalCards = 0;
                
                Object.values(this.deck).forEach(card => {
                    if (card.count > 0) {
                        totalCards += card.count;
                        if (card.type_line?.toLowerCase().includes('land')) lands += card.count;
                        if (card.type_line?.toLowerCase().includes('creature')) creatures += card.count;
                        
                        const cmc = card.cmc || 0;
                        totalCMC += cmc * card.count;
                    }
                });
                
                document.getElementById('lands').textContent = `${lands} Tierras`;
                document.getElementById('creatures').textContent = `${creatures} Criaturas`;
                document.getElementById('avgCMC').textContent = totalCards ? (totalCMC / totalCards).toFixed(1) : '--';
            }

            exportDeck() {
                let exportText = `DECK\n`;
                if (this.currentFormat === 'commander') exportText += `Commander\n`;
                
                Object.values(this.deck).forEach(card => {
                    if (card.count > 0) {
                        exportText += `${card.count} ${card.name}\n`;
                    }
                });
                
                const blob = new Blob([exportText], { type: 'text/plain' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `${this.currentFormat}_deck.txt`;
                a.click();
            }

            clearDeck() {
                this.deck = {};
                this.renderDeck();
            }
        }

        // Initialize app
        const deckBuilder = new MTGDeckBuilder();
    </script>
</body>
</html>
