<!DOCTYPE html>
<html lang="pt-BR" class="dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pelada do Esquadrão - App & Rankings</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: { sans: ['Inter', 'sans-serif'] },
          colors: {
            brand: { 50: '#ecfdf5', 100: '#d1fae5', 500: '#10b981', 600: '#059669', 700: '#047857', 900: '#064e3b' },
            gold: { 400: '#fbbf24', 500: '#f59e0b' }
          }
        }
      }
    }
  </script>
  <script src="./data.js"></script>
  <style>
    ::-webkit-scrollbar { width: 6px; height: 6px; }
    ::-webkit-scrollbar-track { background: #111827; }
    ::-webkit-scrollbar-thumb { background: #374151; border-radius: 9999px; }
    ::-webkit-scrollbar-thumb:hover { background: #059669; }
    .active-tab { color: #10b981; border-top: 3px solid #10b981; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: translateY(0); } }
    .animate-fade-in { animation: fadeIn 0.25s ease-out forwards; }
  </style>
</head>
<body class="bg-gray-950 text-gray-100 font-sans min-h-screen flex flex-col justify-between selection:bg-brand-500 selection:text-white pb-20 md:pb-6">
  <header class="sticky top-0 z-30 bg-gray-900/90 backdrop-blur-md border-b border-gray-800 shadow-lg">
    <div class="max-w-md md:max-w-3xl mx-auto px-4 py-3 flex items-center justify-between">
      <div class="flex items-center space-x-3">
        <div class="w-10 h-10 rounded-full bg-gradient-to-tr from-brand-600 to-amber-400 p-0.5 flex items-center justify-center shadow-md">
          <div class="w-full h-full bg-gray-900 rounded-full flex items-center justify-center">
            <i class="fa-solid fa-futbol text-brand-500 text-lg"></i>
          </div>
        </div>
        <div>
          <h1 class="text-lg font-extrabold tracking-tight text-white leading-none">PELADA DO ESQUADRÃO</h1>
          <p class="text-xs text-gray-400 mt-0.5">Estatísticas & Histórico Oficial</p>
        </div>
      </div>
      <div class="flex items-center gap-1">
        <button id="data-btn" onclick="toggleDataModal()" class="hidden p-2 rounded-lg text-gray-400 hover:text-white hover:bg-gray-800 transition" title="Backup / Opções">
          <i class="fa-solid fa-sliders text-lg"></i>
        </button>
        <button id="auth-btn" onclick="authButtonClick()" class="px-2.5 py-1.5 rounded-lg text-xs font-semibold text-gray-400 hover:text-white hover:bg-gray-800 transition flex items-center gap-1.5" title="Entrar">
          <i class="fa-solid fa-lock"></i> <span id="auth-btn-text">Entrar</span>
        </button>
      </div>
    </div>
    <div class="max-w-md md:max-w-3xl mx-auto px-4 pb-3 grid grid-cols-3 gap-2 text-center">
      <div class="bg-gray-800/60 rounded-xl p-2 border border-gray-700/50">
        <span class="block text-[10px] text-gray-400 uppercase tracking-wider font-semibold">Partidas</span>
        <span id="stat-matches" class="text-lg font-bold text-brand-500">0</span>
      </div>
      <div class="bg-gray-800/60 rounded-xl p-2 border border-gray-700/50">
        <span class="block text-[10px] text-gray-400 uppercase tracking-wider font-semibold">Gols Totais</span>
        <span id="stat-goals" class="text-lg font-bold text-white">0</span>
      </div>
      <div class="bg-gray-800/60 rounded-xl p-2 border border-gray-700/50 truncate">
        <span class="block text-[10px] text-gray-400 uppercase tracking-wider font-semibold truncate">Maior Artilheiro</span>
        <span id="stat-topscorer" class="text-xs md:text-sm font-bold text-amber-400 truncate block mt-1">-</span>
      </div>
    </div>
  </header>

  <main class="max-w-md md:max-w-3xl mx-auto w-full flex-1 px-4 py-4">
    <section id="tab-rankings" class="space-y-4 animate-fade-in">
      <div class="flex items-center justify-between gap-2">
        <div class="relative flex-1">
          <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-gray-400 text-sm"></i>
          <input type="text" id="search-player" placeholder="Buscar jogador..." oninput="renderRankings()" class="w-full bg-gray-900 border border-gray-800 text-sm rounded-xl pl-9 pr-3 py-2 text-white placeholder-gray-500 focus:outline-none focus:border-brand-500 transition">
        </div>
        <select id="sort-ranking" onchange="renderRankings()" class="bg-gray-900 border border-gray-800 text-xs rounded-xl px-3 py-2 text-gray-300 focus:outline-none focus:border-brand-500">
          <option value="goals">Mais Gols</option>
          <option value="wins">Mais Vitórias</option>
          <option value="craque">Mais Craques (MVP)</option>
          <option value="presence">Mais Jogos</option>
          <option value="avg">Média Gols/Jogo</option>
        </select>
      </div>

      <div id="teams-card" class="bg-gray-900 rounded-2xl border border-gray-800 p-3 shadow-xl"></div>
      <p id="avg-note" class="hidden text-[10px] text-gray-500">Média considera apenas jogadores com 5 jogos ou mais.</p>
      <div id="podium-container" class="grid grid-cols-3 gap-2 pt-2 items-end min-h-[160px]"></div>

      <div class="bg-gray-900 rounded-2xl border border-gray-800 overflow-hidden shadow-xl">
        <div class="p-3 bg-gray-900/80 border-b border-gray-800 flex justify-between items-center text-xs font-semibold text-gray-400">
          <span># JOGADOR</span>
          <div class="flex space-x-2 md:space-x-4">
            <span class="w-8 text-center" title="Partidas">JOG</span>
            <span class="w-8 text-center text-sky-400" title="Vitórias">VIT</span>
            <span class="w-8 text-center text-amber-400" title="Craque da Partida">⭐</span>
            <span id="col-goals" class="w-8 text-center text-brand-500" title="Gols">GOLS</span>
          </div>
        </div>
        <div id="rankings-list" class="divide-y divide-gray-800/60"></div>
      </div>
    </section>

    <section id="tab-matches" class="hidden space-y-4 animate-fade-in">
      <div class="flex justify-between items-center">
        <h2 class="text-sm font-bold text-gray-400 uppercase tracking-wider">Histórico de Jogos</h2>
        <span id="match-count-badge" class="text-xs bg-brand-900/60 text-brand-500 px-2.5 py-1 rounded-full border border-brand-700/50 font-medium">0 partidas</span>
      </div>
      <div id="matches-list" class="space-y-3"></div>
    </section>

    <section id="tab-add" class="hidden space-y-4 animate-fade-in">
      <div class="bg-gray-900 rounded-2xl border border-gray-800 p-4 shadow-xl">
        <div class="flex justify-between items-center mb-4">
          <h2 id="form-title" class="text-base font-bold text-white flex items-center gap-2">
            <i class="fa-solid fa-circle-plus text-brand-500"></i> Lançar Nova Partida
          </h2>
          <button id="cancel-edit-btn" onclick="resetForm()" class="hidden text-xs text-rose-400 hover:underline">Cancelar Edição</button>
        </div>
        <form id="match-form" onsubmit="saveMatch(event)" class="space-y-4">
          <input type="hidden" id="match-id" value="">
          <div>
            <label class="block text-xs font-semibold text-gray-400 mb-1">Data da Pelada</label>
            <input type="date" id="match-date" required class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white focus:outline-none focus:border-brand-500">
          </div>
          <div class="grid grid-cols-2 gap-3 bg-gray-950 p-3 rounded-xl border border-gray-800/80">
            <div class="text-center space-y-2">
              <span class="inline-block px-2 py-0.5 text-xs font-bold bg-white text-gray-900 rounded">TIME BRANCO</span>
              <div>
                <label class="block text-[10px] text-gray-500 mb-1">Gols Marcados</label>
                <input type="number" min="0" id="score-white" value="0" required class="w-full text-center bg-gray-900 border border-gray-800 text-lg font-bold rounded-lg py-1.5 text-white focus:outline-none focus:border-brand-500">
              </div>
            </div>
            <div class="text-center space-y-2">
              <span class="inline-block px-2 py-0.5 text-xs font-bold bg-gray-800 text-gray-200 border border-gray-700 rounded">TIME PRETO</span>
              <div>
                <label class="block text-[10px] text-gray-500 mb-1">Gols Marcados</label>
                <input type="number" min="0" id="score-black" value="0" required class="w-full text-center bg-gray-900 border border-gray-800 text-lg font-bold rounded-lg py-1.5 text-white focus:outline-none focus:border-brand-500">
              </div>
            </div>
          </div>
          <div>
            <label class="block text-xs font-semibold text-gray-400 mb-1">Gols da Partida (Separados por vírgula)</label>
            <input type="text" id="match-goals" placeholder="Ex: João (2), Pedro (1), Lucas" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white placeholder-gray-600 focus:outline-none focus:border-brand-500">
            <p class="text-[10px] text-gray-500 mt-1">Dica: Escreva 'Nome (Qtd)' ou apenas o nome várias vezes.</p>
          </div>
          <div>
            <label class="block text-xs font-semibold text-amber-400 mb-1">👑 Craque da Partida (MVP)</label>
            <input type="text" id="match-mvp" placeholder="Nome do jogador eleito" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white placeholder-gray-600 focus:outline-none focus:border-amber-500">
          </div>
          <div>
            <label class="block text-xs font-semibold text-gray-400 mb-1">Lista de Presença (Separados por vírgula)</label>
            <textarea id="match-players" rows="3" placeholder="João, Pedro, Lucas, Mateus, Gabriel, Marcos..." class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white placeholder-gray-600 focus:outline-none focus:border-brand-500"></textarea>
            <p class="text-[10px] text-gray-500 mt-1">Todos nesta lista somarão +1 no total de jogos.</p>
          </div>
          <button type="submit" class="w-full py-3 bg-brand-600 hover:bg-brand-500 text-white font-bold rounded-xl shadow-lg shadow-brand-900/40 transition flex items-center justify-center gap-2">
            <i class="fa-solid fa-floppy-disk"></i> <span id="btn-submit-text">Salvar Partida</span>
          </button>
        </form>
      </div>
    </section>
  </main>

  <nav class="fixed bottom-0 left-0 right-0 z-40 bg-gray-900/95 backdrop-blur-lg border-t border-gray-800">
    <div class="max-w-md md:max-w-3xl mx-auto flex justify-around">
      <button onclick="switchTab('rankings')" id="nav-rankings" class="flex-1 py-3 flex flex-col items-center gap-1 active-tab transition">
        <i class="fa-solid fa-trophy text-lg"></i>
        <span class="text-[10px] font-medium">Rankings</span>
      </button>
      <button onclick="switchTab('matches')" id="nav-matches" class="flex-1 py-3 flex flex-col items-center gap-1 text-gray-500 hover:text-gray-300 transition">
        <i class="fa-solid fa-calendar-days text-lg"></i>
        <span class="text-[10px] font-medium">Partidas</span>
      </button>
      <button onclick="switchTab('add')" id="nav-add" class="hidden flex-1 py-3 flex flex-col items-center gap-1 text-gray-500 hover:text-gray-300 transition">
        <i class="fa-solid fa-plus-circle text-lg"></i>
        <span class="text-[10px] font-medium">Lançar</span>
      </button>
    </div>
  </nav>

  <div id="data-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-gray-900 rounded-2xl border border-gray-800 w-full max-w-md p-5 space-y-4 shadow-2xl">
      <div class="flex justify-between items-center border-b border-gray-800 pb-3">
        <h3 class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-database text-brand-500"></i> Gerenciar Dados
        </h3>
        <button onclick="toggleDataModal()" class="text-gray-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
      </div>
      <p class="text-xs text-gray-400">Você pode exportar os dados para compartilhar com o grupo ou restaurar um backup existente.</p>
      <div class="space-y-2">
        <button onclick="exportData()" class="w-full py-2.5 bg-gray-800 hover:bg-gray-700 text-white text-xs font-semibold rounded-xl border border-gray-700 flex items-center justify-center gap-2">
          <i class="fa-solid fa-download"></i> Exportar Dados (Baixar JSON)
        </button>
        <label class="w-full py-2.5 bg-gray-800 hover:bg-gray-700 text-white text-xs font-semibold rounded-xl border border-gray-700 flex items-center justify-center gap-2 cursor-pointer">
          <i class="fa-solid fa-upload"></i> Importar / Restaurar JSON
          <input type="file" id="import-file" accept=".json" onchange="importData(event)" class="hidden">
        </label>
      </div>
    </div>
  </div>

  <div id="login-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden flex items-center justify-center p-4">
    <div class="bg-gray-900 rounded-2xl border border-gray-800 w-full max-w-sm p-5 space-y-4 shadow-2xl">
      <div class="flex justify-between items-center border-b border-gray-800 pb-3">
        <h3 id="login-title" class="text-base font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-lock text-brand-500"></i> Entrar
        </h3>
        <button onclick="closeLogin()" class="text-gray-400 hover:text-white"><i class="fa-solid fa-xmark text-lg"></i></button>
      </div>
      <p class="text-xs text-gray-400">Acesso restrito ao administrador da pelada. Todos podem ver rankings e partidas sem login.</p>
      <form id="login-form" onsubmit="submitLogin(event)" class="space-y-3">
        <input type="email" id="login-email" required autocomplete="email" placeholder="E-mail" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white placeholder-gray-600 focus:outline-none focus:border-brand-500">
        <input type="password" id="login-password" required minlength="6" autocomplete="current-password" placeholder="Senha" class="w-full bg-gray-950 border border-gray-800 rounded-xl p-2.5 text-sm text-white placeholder-gray-600 focus:outline-none focus:border-brand-500">
        <p id="login-error" class="hidden text-xs text-rose-400"></p>
        <button type="submit" id="login-submit" class="w-full py-2.5 bg-brand-600 hover:bg-brand-700 text-white text-sm font-bold rounded-xl transition">Entrar</button>
      </form>
      <div class="flex justify-between text-xs">
        <button id="login-toggle" onclick="toggleSignupMode()" class="text-brand-500 hover:underline">Primeiro acesso? Criar conta</button>
      </div>
    </div>
  </div>

  <div id="toast" class="fixed top-4 right-4 z-50 bg-brand-600 text-white text-xs font-semibold px-4 py-3 rounded-xl shadow-xl flex items-center gap-2 transition-all duration-300 translate-y-[-100px] opacity-0 pointer-events-none">
    <i class="fa-solid fa-circle-check text-base"></i>
    <span id="toast-message">Operação realizada com sucesso!</span>
  </div>

  <script>
    let matches = [];
    let isAdmin = false;

    function getLocalMatches() {
      try {
        const saved = JSON.parse(localStorage.getItem('esquadrao_pelada_data') || '[]');
        return Array.isArray(saved) ? saved : [];
      } catch (e) {
        return [];
      }
    }

    function persistMatches(list) {
      localStorage.setItem('esquadrao_pelada_data', JSON.stringify(list));
    }

    async function loadMatches() {
      matches = getLocalMatches();
      updateApp();
    }

    async function api(url, method, body) {
      const methodName = (method || 'GET').toUpperCase();
      if (url.startsWith('/api/matches')) {
        const existing = getLocalMatches();

        if (methodName === 'GET') return existing;
        if (methodName === 'POST') {
          const next = { ...(body || {}), id: body?.id || `m_${Date.now()}_${Math.random().toString(16).slice(2)}` };
          const list = [...existing, next];
          persistMatches(list);
          matches = list;
          return next;
        }
        if (methodName === 'PUT') {
          if (url === '/api/matches') {
            const list = Array.isArray(body) ? body : (body ? [body] : []);
            persistMatches(list);
            matches = list;
            return list;
          }
          const id = url.split('/').pop();
          const list = existing.map(item => item.id === id ? { ...item, ...(body || {}) } : item);
          persistMatches(list);
          matches = list;
          return list.find(item => item.id === id);
        }
        if (methodName === 'DELETE') {
          const id = url.split('/').pop();
          const list = existing.filter(item => item.id !== id);
          persistMatches(list);
          matches = list;
          return null;
        }
      }

      throw new Error('Operação não disponível localmente.');
    }

    function setAdmin(value, email) {
      isAdmin = value;
      document.getElementById('nav-add').classList.toggle('hidden', !isAdmin);
      document.getElementById('data-btn').classList.toggle('hidden', !isAdmin);
      document.getElementById('auth-btn-text').innerText = isAdmin ? 'Sair' : 'Entrar';
      document.getElementById('auth-btn').title = isAdmin ? `Logado como ${email}` : 'Entrar';
      document.querySelector('#auth-btn i').className = isAdmin ? 'fa-solid fa-right-from-bracket' : 'fa-solid fa-lock';
      if (!isAdmin && !document.getElementById('tab-add').classList.contains('hidden')) switchTab('rankings');
      renderMatches();
    }

    window.onload = function () {
      const today = new Date().toISOString().split('T')[0];
      document.getElementById('match-date').value = today;
      loadMatches();
      const savedUser = JSON.parse(localStorage.getItem('esquadrao_auth') || 'null');
      if (savedUser && savedUser.email && savedUser.password) {
        setAdmin(savedUser.email === 'caetanobiazus02@gmail.com', savedUser.email);
      }
    };

    function switchTab(tabName) {
      if (tabName === 'add' && !isAdmin) { openLogin(); return; }
      const tabs = ['rankings', 'matches', 'add'];
      tabs.forEach(tab => {
        const section = document.getElementById(`tab-${tab}`);
        const navBtn = document.getElementById(`nav-${tab}`);
        if (tab === tabName) {
          section.classList.remove('hidden');
          navBtn.classList.add('active-tab');
          navBtn.classList.remove('text-gray-500');
        } else {
          section.classList.add('hidden');
          navBtn.classList.remove('active-tab');
          navBtn.classList.add('text-gray-500');
        }
      });
    }

    function getPlayerStats() {
      const players = {};
      if (window.HISTORICO && Array.isArray(window.HISTORICO.players)) {
        window.HISTORICO.players.forEach(h => {
          players[h.name.toLowerCase()] = { name: h.name, goals: h.goals || 0, presence: h.presence || 0, wins: h.wins || 0, craque: h.craque || 0 };
        });
      }

      matches.forEach(m => {
        const matchPlayers = (m.players || '').split(',').map(p => p.trim()).filter(Boolean);
        matchPlayers.forEach(pName => {
          const key = pName.toLowerCase();
          if (!players[key]) players[key] = { name: pName, goals: 0, presence: 0, wins: 0, craque: 0 };
          players[key].presence += 1;
        });

        if (m.mvp && m.mvp.trim()) {
          const key = m.mvp.trim().toLowerCase();
          if (!players[key]) players[key] = { name: m.mvp.trim(), goals: 0, presence: 0, wins: 0, craque: 0 };
          players[key].craque += 1;
        }

        if (m.goalsDetail && m.goalsDetail.trim()) {
          const parts = m.goalsDetail.split(',');
          parts.forEach(part => {
            const raw = part.trim();
            if (!raw) return;
            const matchCount = raw.match(/\((\d+)\)/);
            let gCount = 1;
            let pName = raw;
            if (matchCount) {
              gCount = parseInt(matchCount[1], 10) || 1;
              pName = raw.replace(/\(\d+\)/, '').trim();
            }
            if (pName) {
              const key = pName.toLowerCase();
              if (!players[key]) players[key] = { name: pName, goals: 0, presence: 0, wins: 0, craque: 0 };
              players[key].goals += gCount;
            }
          });
        }
      });

      return Object.values(players).map(p => ({ ...p, avg: p.presence > 0 ? p.goals / p.presence : 0 }));
    }

    function formatAvg(value) { return Number(value || 0).toFixed(2).replace('.', ','); }

    function getTeamStats() {
      const t = window.HISTORICO && window.HISTORICO.teams ? { ...window.HISTORICO.teams } : { branco: 0, preto: 0, empate: 0 };
      matches.forEach(m => {
        const w = parseInt(m.scoreWhite, 10) || 0;
        const b = parseInt(m.scoreBlack, 10) || 0;
        if (w > b) t.branco += 1;
        else if (b > w) t.preto += 1;
        else t.empate += 1;
      });
      return t;
    }

    function renderTeams() {
      const t = getTeamStats();
      const total = t.branco + t.preto + t.empate || 1;
      const pct = n => (n / total * 100).toFixed(1);
      document.getElementById('teams-card').innerHTML = `
        <div class="flex justify-between items-center text-xs font-bold mb-2">
          <span class="flex items-center gap-1.5"><span class="px-2 py-0.5 bg-white text-gray-900 rounded">BRANCO</span><span class="text-white text-base">${t.branco}</span></span>
          <span class="text-gray-400 font-semibold">Empates <span class="text-gray-200">${t.empate}</span></span>
          <span class="flex items-center gap-1.5"><span class="text-white text-base">${t.preto}</span><span class="px-2 py-0.5 bg-gray-800 text-gray-200 border border-gray-700 rounded">PRETO</span></span>
        </div>
        <div class="flex h-2.5 rounded-full overflow-hidden bg-gray-800 gap-0.5">
          <div class="bg-gray-100" style="width:${pct(t.branco)}%"></div>
          <div class="bg-gray-600" style="width:${pct(t.empate)}%"></div>
          <div class="bg-gray-950 border border-gray-600 rounded-r-full" style="width:${pct(t.preto)}%"></div>
        </div>
        <p class="text-[10px] text-gray-500 mt-1.5 text-center">Vitórias por time</p>
      `;
    }

    function updateApp() {
      renderStats();
      renderRankings();
      renderMatches();
    }

    function renderStats() {
      const t = getTeamStats();
      const totalMatches = t.branco + t.preto + t.empate;
      document.getElementById('stat-matches').innerText = totalMatches;

      let totalGoals = (window.HISTORICO && window.HISTORICO.ownGoals) || 0;
      if (window.HISTORICO && Array.isArray(window.HISTORICO.players)) {
        totalGoals += window.HISTORICO.players.reduce((sum, p) => sum + (p.goals || 0), 0);
      }
      matches.forEach(m => {
        totalGoals += (parseInt(m.scoreWhite, 10) || 0) + (parseInt(m.scoreBlack, 10) || 0);
      });
      document.getElementById('stat-goals').innerText = totalGoals;

      const stats = getPlayerStats().sort((a, b) => b.goals - a.goals);
      const topScorerEl = document.getElementById('stat-topscorer');
      if (stats.length > 0 && stats[0].goals > 0) {
        topScorerEl.innerText = `${stats[0].name} (${stats[0].goals})`;
      } else {
        topScorerEl.innerText = '-';
      }

      const listed = (window.PARTIDAS || []).length + matches.length;
      document.getElementById('match-count-badge').innerText = `${listed} partida${listed === 1 ? '' : 's'}`;
      renderTeams();
    }

    function renderRankings() {
      const search = (document.getElementById('search-player').value || '').toLowerCase();
      const sortBy = document.getElementById('sort-ranking').value;

      let stats = getPlayerStats();
      if (search) stats = stats.filter(s => s.name.toLowerCase().includes(search));

      document.getElementById('avg-note').classList.toggle('hidden', sortBy !== 'avg');
      document.getElementById('col-goals').innerText = sortBy === 'avg' ? 'MÉD' : 'GOLS';
      if (sortBy === 'avg') {
        stats = stats.filter(s => s.presence >= 5);
      }

      stats.sort((a, b) => {
        if (b[sortBy] !== a[sortBy]) return b[sortBy] - a[sortBy];
        return b.goals - a.goals;
      });

      const podiumContainer = document.getElementById('podium-container');
      podiumContainer.innerHTML = '';
      if (stats.length > 0 && !search) {
        const top3 = [stats[1], stats[0], stats[2]];
        const ranks = [2, 1, 3];
        const heights = ['h-28', 'h-36', 'h-24'];
        const colors = ['border-gray-400 bg-gray-800/80', 'border-amber-400 bg-amber-950/40', 'border-amber-700 bg-amber-900/30'];
        const badges = ['🥈 2º', '👑 1º', '🥉 3º'];
        top3.forEach((player, idx) => {
          if (!player) return;
          const podEl = document.createElement('div');
          podEl.className = `flex flex-col items-center justify-end rounded-t-2xl border-t-2 border-x border-gray-800 p-2 text-center shadow-lg transition-all ${heights[idx]} ${colors[idx]}`;
          podEl.innerHTML = `
            <span class="text-[10px] font-bold px-2 py-0.5 rounded-full bg-black/50 text-amber-300 mb-1">${badges[idx]}</span>
            <span class="text-xs font-extrabold text-white truncate max-w-[80px]">${player.name}</span>
            <div class="mt-1 flex items-center gap-1 text-[11px]">
              ${sortBy === 'avg' ? `<span class="text-brand-400 font-bold">${formatAvg(player.avg)}/jogo</span>` : sortBy === 'wins' ? `<span class="text-sky-400 font-bold">${player.wins}V</span><span class="text-gray-500">•</span><span class="text-gray-400 font-bold">${player.presence}J</span>` : `<span class="text-brand-400 font-bold">${player.goals}G</span><span class="text-gray-500">•</span><span class="text-amber-400 font-bold">${player.craque}⭐</span>`}
            </div>
          `;
          podiumContainer.appendChild(podEl);
        });
      }

      const listContainer = document.getElementById('rankings-list');
      listContainer.innerHTML = '';
      if (stats.length === 0) {
        listContainer.innerHTML = '<p class="text-center text-xs text-gray-500 py-6">Nenhum jogador encontrado.</p>';
        return;
      }

      stats.forEach((p, index) => {
        const row = document.createElement('div');
        row.className = 'p-3 flex justify-between items-center hover:bg-gray-800/40 transition text-sm';
        let rankBadge = `<span class="w-6 text-xs text-gray-500 font-bold">${index + 1}</span>`;
        if (index === 0) rankBadge = '<span class="w-6 text-xs text-amber-400 font-extrabold">🥇</span>';
        if (index === 1) rankBadge = '<span class="w-6 text-xs text-gray-300 font-extrabold">🥈</span>';
        if (index === 2) rankBadge = '<span class="w-6 text-xs text-amber-600 font-extrabold">🥉</span>';

        row.innerHTML = `
          <div class="flex items-center space-x-2 truncate">
            ${rankBadge}
            <div class="w-7 h-7 rounded-full bg-gray-800 border border-gray-700 flex items-center justify-center font-bold text-xs text-gray-300">${p.name.charAt(0).toUpperCase()}</div>
            <span class="font-semibold text-gray-100 truncate text-xs md:text-sm">${p.name}</span>
          </div>
          <div class="flex space-x-2 md:space-x-4 text-xs font-mono shrink-0">
            <span class="w-8 text-center text-gray-400">${p.presence}</span>
            <span class="w-8 text-center text-sky-400 font-bold">${p.wins}</span>
            <span class="w-8 text-center text-amber-400 font-bold">${p.craque}</span>
            <span class="w-8 text-center text-brand-400 font-bold" ${sortBy === 'avg' ? `title="${formatAvg(p.avg)} gols/jogo"` : ''}>${sortBy === 'avg' ? formatAvg(p.avg) : p.goals}</span>
          </div>
        `;
        listContainer.appendChild(row);
      });
    }

    function goalsList(list) {
      if (!list || !list.length) return '<span class="text-gray-600">-</span>';
      return list.map(([name, n]) => `${name}${n > 1 ? ` <span class="text-brand-500 font-bold">(${n})</span>` : ''}`).join(', ');
    }

    function renderOfficialMatch(m) {
      const card = document.createElement('div');
      card.className = 'bg-gray-900 rounded-2xl border border-gray-800 p-4 shadow-lg hover:border-gray-700 transition space-y-3';
      const winBadge = m.winner === 'Empate'
        ? '<span class="text-[10px] font-bold px-2 py-0.5 rounded bg-gray-700 text-gray-200">EMPATE</span>'
        : m.winner === 'Branco'
          ? '<span class="text-[10px] font-bold px-2 py-0.5 rounded bg-white text-gray-900">VITÓRIA BRANCO</span>'
          : '<span class="text-[10px] font-bold px-2 py-0.5 rounded bg-gray-950 text-gray-200 border border-gray-600">VITÓRIA PRETO</span>';
      const side = (label, score, won) => `
        <div class="text-center">
          <span class="text-[10px] font-bold uppercase text-gray-400 block mb-0.5">${label}</span>
          <span class="text-2xl font-black ${won ? 'text-brand-500' : 'text-white'}">${score}</span>
        </div>`;
      card.innerHTML = `
        <div class="flex justify-between items-center border-b border-gray-800/80 pb-2">
          <span class="text-xs text-gray-400 font-medium"><i class="fa-regular fa-calendar text-brand-500 mr-1"></i> ${m.date} <span class="text-gray-600 ml-1">#${m.n}</span></span>
          ${winBadge}
        </div>
        <div class="flex items-center justify-around py-1">
          ${side('Branco', m.white, m.winner === 'Branco')}
          <span class="text-xs font-extrabold text-gray-600 uppercase">VS</span>
          ${side('Preto', m.black, m.winner === 'Preto')}
        </div>
        <div class="space-y-1.5 text-xs border-t border-gray-800/80 pt-2.5">
          ${m.craque ? `<div class="flex items-center gap-1.5 text-amber-400 font-semibold"><span>👑 Craque:</span><span class="text-gray-200">${m.craque}</span></div>` : ''}
          <div class="text-gray-400"><span class="text-brand-500">⚽ Branco:</span> <span class="text-gray-300">${goalsList(m.goalsWhite)}</span></div>
          <div class="text-gray-400"><span class="text-brand-500">⚽ Preto:</span> <span class="text-gray-300">${goalsList(m.goalsBlack)}</span></div>
        </div>
        <details class="text-xs border-t border-gray-800/80 pt-2.5">
          <summary class="cursor-pointer text-gray-500 hover:text-gray-300 select-none">Ver escalações</summary>
          <div class="grid grid-cols-2 gap-3 mt-2">
            <div>
              <span class="text-[10px] font-bold px-2 py-0.5 rounded bg-white text-gray-900">BRANCO</span>
              <ul class="mt-1.5 space-y-0.5 text-gray-300">${(m.teamWhite || []).map(p => `<li>${p}</li>`).join('')}</ul>
            </div>
            <div>
              <span class="text-[10px] font-bold px-2 py-0.5 rounded bg-gray-800 text-gray-200 border border-gray-700">PRETO</span>
              <ul class="mt-1.5 space-y-0.5 text-gray-300">${(m.teamBlack || []).map(p => `<li>${p}</li>`).join('')}</ul>
            </div>
          </div>
        </details>
      `;
      return card;
    }

    function renderMatches() {
      const container = document.getElementById('matches-list');
      container.innerHTML = '';

      if (matches.length > 0) {
        container.insertAdjacentHTML('beforeend', '<h3 class="text-[11px] font-bold text-gray-500 uppercase tracking-wider pt-1">Lançadas no app</h3>');
      }

      const sorted = [...matches].sort((a, b) => new Date(b.date) - new Date(a.date));
      sorted.forEach(m => {
        const card = document.createElement('div');
        card.className = 'bg-gray-900 rounded-2xl border border-gray-800 p-4 shadow-lg hover:border-gray-700 transition space-y-3';
        const dateParts = m.date.split('-');
        const formattedDate = dateParts.length === 3 ? `${dateParts[2]}/${dateParts[1]}/${dateParts[0]}` : m.date;
        card.innerHTML = `
          <div class="flex justify-between items-center border-b border-gray-800/80 pb-2">
            <span class="text-xs text-gray-400 font-medium"><i class="fa-regular fa-calendar text-brand-500 mr-1"></i> ${formattedDate}</span>
            <div class="flex space-x-2 ${isAdmin ? '' : 'hidden'}">
              <button onclick="editMatch('${m.id}')" class="text-xs text-gray-400 hover:text-brand-400 transition" title="Editar"><i class="fa-solid fa-pen"></i></button>
              <button onclick="deleteMatch('${m.id}')" class="text-xs text-gray-400 hover:text-rose-400 transition" title="Excluir"><i class="fa-solid fa-trash"></i></button>
            </div>
          </div>
          <div class="flex items-center justify-around py-1">
            <div class="text-center"><span class="text-[10px] font-bold uppercase text-gray-400 block mb-0.5">Branco</span><span class="text-2xl font-black text-white">${m.scoreWhite}</span></div>
            <span class="text-xs font-extrabold text-gray-600 uppercase">VS</span>
            <div class="text-center"><span class="text-[10px] font-bold uppercase text-gray-400 block mb-0.5">Preto</span><span class="text-2xl font-black text-white">${m.scoreBlack}</span></div>
          </div>
          <div class="space-y-1.5 text-xs border-t border-gray-800/80 pt-2.5">
            ${m.mvp ? `<div class="flex items-center gap-1.5 text-amber-400 font-semibold"><span>👑 Craque:</span><span class="text-gray-200">${m.mvp}</span></div>` : ''}
            ${m.goalsDetail ? `<div class="flex items-start gap-1.5 text-gray-400"><span class="text-brand-500">⚽ Gols:</span><span class="text-gray-300">${m.goalsDetail}</span></div>` : ''}
          </div>
        `;
        container.appendChild(card);
      });

      const official = Array.isArray(window.PARTIDAS) ? window.PARTIDAS : [];
      if (official.length) {
        container.insertAdjacentHTML('beforeend', '<h3 class="text-[11px] font-bold text-gray-500 uppercase tracking-wider pt-1">Planilha oficial</h3>');
        [...official].reverse().forEach(m => container.appendChild(renderOfficialMatch(m)));
      }
    }

    async function saveMatch(e) {
      e.preventDefault();
      const matchId = document.getElementById('match-id').value;
      const data = {
        date: document.getElementById('match-date').value,
        scoreWhite: document.getElementById('score-white').value,
        scoreBlack: document.getElementById('score-black').value,
        goalsDetail: document.getElementById('match-goals').value,
        mvp: document.getElementById('match-mvp').value,
        players: document.getElementById('match-players').value
      };

      try {
        if (matchId) {
          await api(`/api/matches/${matchId}`, 'PUT', data);
          showToast('Partida atualizada com sucesso!');
        } else {
          await api('/api/matches', 'POST', data);
          showToast('Partida cadastrada com sucesso!');
        }
      } catch (err) {
        alert(err.message);
        return;
      }

      resetForm();
      await loadMatches();
      switchTab('matches');
    }

    function editMatch(id) {
      const match = matches.find(m => m.id === id);
      if (!match) return;
      document.getElementById('match-id').value = match.id;
      document.getElementById('match-date').value = match.date;
      document.getElementById('score-white').value = match.scoreWhite;
      document.getElementById('score-black').value = match.scoreBlack;
      document.getElementById('match-goals').value = match.goalsDetail || '';
      document.getElementById('match-mvp').value = match.mvp || '';
      document.getElementById('match-players').value = match.players || '';
      document.getElementById('form-title').innerHTML = '<i class="fa-solid fa-pen-to-square text-amber-400"></i> Editar Partida';
      document.getElementById('btn-submit-text').innerText = 'Atualizar Partida';
      document.getElementById('cancel-edit-btn').classList.remove('hidden');
      switchTab('add');
    }

    async function deleteMatch(id) {
      if (!confirm('Tem certeza que deseja excluir esta partida?')) return;
      try {
        await api(`/api/matches/${id}`, 'DELETE');
        showToast('Partida excluída.');
        loadMatches();
      } catch (err) {
        alert(err.message);
      }
    }

    function resetForm() {
      document.getElementById('match-id').value = '';
      document.getElementById('match-form').reset();
      const today = new Date().toISOString().split('T')[0];
      document.getElementById('match-date').value = today;
      document.getElementById('form-title').innerHTML = '<i class="fa-solid fa-circle-plus text-brand-500"></i> Lançar Nova Partida';
      document.getElementById('btn-submit-text').innerText = 'Salvar Partida';
      document.getElementById('cancel-edit-btn').classList.add('hidden');
    }

    function toggleDataModal() {
      const modal = document.getElementById('data-modal');
      modal.classList.toggle('hidden');
    }

    function exportData() {
      const dataStr = 'data:text/json;charset=utf-8,' + encodeURIComponent(JSON.stringify(matches, null, 2));
      const dlAnchor = document.createElement('a');
      dlAnchor.setAttribute('href', dataStr);
      dlAnchor.setAttribute('download', `pelada_esquadrao_backup_${new Date().toISOString().split('T')[0]}.json`);
      document.body.appendChild(dlAnchor);
      dlAnchor.click();
      dlAnchor.remove();
      showToast('Download do backup iniciado!');
    }

    function importData(event) {
      const file = event.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = async function (e) {
        let imported;
        try {
          imported = JSON.parse(e.target.result);
        } catch (err) {
          alert('Erro ao ler o arquivo JSON.');
          return;
        }
        if (!Array.isArray(imported)) {
          alert('Formato de arquivo inválido.');
          return;
        }
        if (!confirm(`Isso substitui todas as partidas lançadas no site por ${imported.length} partida(s) do arquivo. Continuar?`)) return;
        try {
          matches = await api('/api/matches', 'PUT', imported);
          updateApp();
          toggleDataModal();
          showToast('Dados importados com sucesso!');
        } catch (err) {
          alert(err.message);
        }
      };
      event.target.value = '';
      reader.readAsText(file);
    }

    function showToast(message) {
      const toast = document.getElementById('toast');
      document.getElementById('toast-message').innerText = message;
      toast.classList.remove('translate-y-[-100px]', 'opacity-0', 'pointer-events-none');
      setTimeout(() => {
        toast.classList.add('translate-y-[-100px]', 'opacity-0', 'pointer-events-none');
      }, 3000);
    }

    const ADMIN_EMAIL = 'caetanobiazus02@gmail.com';
    const ADMIN_PASSWORD = 'esquadrao123';
    let signupMode = false;

    function openLogin() {
      document.getElementById('login-error').innerText = '';
      document.getElementById('login-error').classList.add('hidden');
      document.getElementById('login-modal').classList.remove('hidden');
      document.getElementById('login-email').focus();
    }

    function closeLogin() {
      document.getElementById('login-modal').classList.add('hidden');
    }

    function authButtonClick() {
      if (!isAdmin) return openLogin();
      localStorage.removeItem('esquadrao_auth');
      setAdmin(false, '');
      showToast('Você saiu.');
    }

    function toggleSignupMode() {
      signupMode = !signupMode;
      document.getElementById('login-error').innerText = '';
      document.getElementById('login-error').classList.add('hidden');
      document.getElementById('login-title').innerHTML = `<i class="fa-solid fa-${signupMode ? 'user-plus' : 'lock'} text-brand-500"></i> ${signupMode ? 'Criar conta' : 'Entrar'}`;
      document.getElementById('login-submit').innerText = signupMode ? 'Criar conta' : 'Entrar';
      document.getElementById('login-toggle').innerText = signupMode ? 'Já tenho conta. Entrar' : 'Primeiro acesso? Criar conta';
      document.getElementById('login-password').autocomplete = signupMode ? 'new-password' : 'current-password';
    }

    function submitLogin(e) {
      e.preventDefault();
      const email = document.getElementById('login-email').value.trim();
      const password = document.getElementById('login-password').value;
      const errorEl = document.getElementById('login-error');

      if (email.toLowerCase() !== ADMIN_EMAIL) {
        errorEl.innerText = 'Este e-mail não tem permissão para editar.';
        errorEl.classList.remove('hidden');
        return;
      }

      if (signupMode) {
        if (!password || password.length < 6) {
          errorEl.innerText = 'A senha precisa ter pelo menos 6 caracteres.';
          errorEl.classList.remove('hidden');
          return;
        }
        localStorage.setItem('esquadrao_auth', JSON.stringify({ email, password }));
        setAdmin(true, email);
        closeLogin();
        showToast('Conta criada! Você já pode editar.');
        document.getElementById('login-form').reset();
        return;
      }

      if (password !== ADMIN_PASSWORD) {
        errorEl.innerText = 'E-mail ou senha incorretos.';
        errorEl.classList.remove('hidden');
        return;
      }

      localStorage.setItem('esquadrao_auth', JSON.stringify({ email, password }));
      setAdmin(true, email);
      closeLogin();
      showToast('Login feito! Agora você pode editar.');
      document.getElementById('login-form').reset();
    }

    window.openLogin = openLogin;
    window.closeLogin = closeLogin;
    window.authButtonClick = authButtonClick;
    window.toggleSignupMode = toggleSignupMode;
    window.submitLogin = submitLogin;
    window.toggleDataModal = toggleDataModal;
    window.exportData = exportData;
    window.importData = importData;
    window.showToast = showToast;
    window.switchTab = switchTab;
    window.saveMatch = saveMatch;
    window.editMatch = editMatch;
    window.deleteMatch = deleteMatch;
    window.resetForm = resetForm;
  </script>
</body>
</html>
