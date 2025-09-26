<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="utf-8" />
  <title>NeuroLink Earth — Startup Pro</title>
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
  <style>
    :root{
      --bg:#0f1724;
      --card:#0b1220;
      --accent:#06b6d4;
      --muted:#94a3b8;
      --glass: rgba(255,255,255,0.03);
      --glass-2: rgba(255,255,255,0.02);
    }
    *{box-sizing:border-box}
    body{
      margin:0;font-family:Inter,system-ui,Segoe UI,Arial;
      background:linear-gradient(180deg,#071026 0%, #071a2b 100%); color:#e6eef6;
      -webkit-font-smoothing:antialiased;
    }
    header{
      padding:18px 20px; display:flex; gap:20px; align-items:center; justify-content:space-between;
      border-bottom:1px solid rgba(255,255,255,0.03);
      background: linear-gradient(90deg, rgba(255,255,255,0.02), transparent);
      position:sticky; top:0; z-index:10;
    }
    header .brand{display:flex; gap:12px; align-items:center}
    .logo{width:44px;height:44px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#8b5cf6); display:flex;align-items:center;justify-content:center;font-weight:700;color:#041022}
    header h1{font-size:18px;margin:0}
    main{padding:20px; max-width:1200px; margin:20px auto}
    .grid{display:grid; grid-template-columns: 320px 1fr; gap:20px}
    /* Left column */
    .panel{background:linear-gradient(180deg,var(--card), rgba(255,255,255,0.02)); padding:16px; border-radius:12px; box-shadow: 0 6px 20px rgba(2,6,23,0.6)}
    .controls input, .controls select {width:100%; padding:10px;border-radius:8px;border:1px solid rgba(255,255,255,0.04); background:transparent; color:inherit;margin-bottom:8px}
    .controls button{width:100%; padding:10px;border-radius:10px;border:none;background:var(--accent); color:#031321; font-weight:700; cursor:pointer}
    .users-list{margin-top:12px; display:flex; flex-direction:column; gap:10px; max-height:52vh; overflow:auto; padding-right:6px}
    .user-item{display:flex; gap:10px; align-items:center; padding:8px;border-radius:10px; cursor:pointer; transition:transform .12s; border:1px solid rgba(255,255,255,0.02)}
    .user-item:hover{transform:translateY(-3px)}
    .avatar{width:44px;height:44px;border-radius:10px;background:linear-gradient(135deg,#6ee7b7,#60a5fa);display:flex;align-items:center;justify-content:center;color:#042029;font-weight:700}
    .user-meta small{color:var(--muted)}
    .selected{outline:2px solid rgba(6,182,212,0.12); box-shadow:0 8px 30px rgba(6,182,212,0.06)}
    /* Right column */
    .top-row{display:flex; gap:16px; margin-bottom:16px}
    .kpi{flex:1; padding:14px; border-radius:10px; background:linear-gradient(180deg, rgba(255,255,255,0.02), transparent); text-align:center}
    .kpi h3{margin:0;font-size:14px;color:var(--muted)}
    .kpi .val{font-size:22px;margin-top:6px}
    .dashboard{display:grid; grid-template-columns: 1fr 420px; gap:20px}
    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01)); padding:16px; border-radius:12px}
    .chart-wrap{display:flex; gap:16px; align-items:center}
    .small-legend{display:flex; gap:8px; align-items:center; color:var(--muted); font-size:13px}
    .leaderboard{display:flex; flex-direction:column; gap:8px}
    .leader-item{display:flex; justify-content:space-between; padding:8px; border-radius:8px; background:var(--glass)}
    footer{margin-top:18px; text-align:center; color:var(--muted); font-size:13px}
    /* responsive */
    @media(max-width:900px){
      .grid{grid-template-columns: 1fr; }
      .dashboard{grid-template-columns:1fr}
      .top-row{flex-direction:column}
    }
  </style>
</head>
<body>
  <header>
    <div class="brand">
      <div class="logo">NL</div>
      <div>
        <h1>NeuroLink Earth</h1>
        <small style="color:var(--muted)">Dashboard · Simulação · Versão Pro</small>
      </div>
    </div>
    <div style="display:flex;gap:12px;align-items:center">
      <div style="text-align:right">
        <div style="font-size:12px;color:var(--muted)">Status</div>
        <div style="font-weight:700;color:#bff3ff">Local • Offline</div>
      </div>
      <button id="exportBtn" title="Exportar dados" style="padding:8px 12px;border-radius:8px;background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--muted);cursor:pointer">Export</button>
    </div>
  </header>

  <main>
    <div class="grid">
      <!-- LEFT -->
      <div>
        <div class="panel">
          <div style="display:flex;justify-content:space-between;align-items:center">
            <h3 style="margin:0">Criar / Selecionar usuário</h3>
            <small style="color:var(--muted)">Persistência local</small>
          </div>

          <div class="controls" style="margin-top:12px">
            <input id="inputId" placeholder="ID (numérico)" type="number" />
            <input id="inputName" placeholder="Nome" />
            <input id="inputEmail" placeholder="Email" />
            <button id="btnCreate">Criar usuário</button>

            <hr style="border:none;height:1px;background:rgba(255,255,255,0.02);margin:12px 0">

            <h4 style="margin:8px 0 6px 0;color:var(--muted)">Atualizar biometria (usuário selecionado)</h4>
            <input id="inputHR" placeholder="Batimentos (60-120)" type="number" />
            <input id="inputSleep" placeholder="Horas de sono (0-12)" type="number" />
            <input id="inputStress" placeholder="Estresse (0-100)" type="number" />
            <button id="btnUpdate">Atualizar biometria</button>
          </div>

          <div style="margin-top:12px">
            <h4 style="margin:6px 0;color:var(--muted)">Usuários</h4>
            <div class="users-list" id="usersList"></div>
          </div>
        </div>

        <div class="panel" style="margin-top:12px">
          <h4 style="margin:0 0 8px 0">Export / Import</h4>
          <div style="display:flex; gap:8px">
            <button id="btnExportJSON">Baixar JSON</button>
            <button id="btnImportJSON">Importar JSON</button>
            <input id="fileInput" type="file" accept=".json" style="display:none" />
          </div>
          <p style="color:var(--muted);font-size:13px;margin-top:8px">Exporta os dados locais para backup ou importar em outro dispositivo.</p>
        </div>
      </div>

      <!-- RIGHT -->
      <div>
        <div class="top-row">
          <div class="kpi card">
            <h3>Usuários</h3>
            <div class="val" id="kpiUsers">0</div>
          </div>
          <div class="kpi card">
            <h3>Média de Foco</h3>
            <div class="val" id="kpiFocus">—</div>
          </div>
          <div class="kpi card">
            <h3>Média de Sono (h)</h3>
            <div class="val" id="kpiSleep">—</div>
          </div>
        </div>

        <div class="dashboard">
          <div class="card">
            <h3 style="margin-top:0">Visão Geral</h3>
            <div style="display:flex;gap:12px;align-items:center">
              <div style="flex:1">
                <canvas id="overviewChart" height="160"></canvas>
              </div>
              <div style="width:220px">
                <h4 style="margin:0 0 8px 0;color:var(--muted)">Ranking de Foco</h4>
                <div class="leaderboard" id="leaderboard"></div>
              </div>
            </div>
          </div>

          <div class="card">
            <h3 style="margin-top:0">Detalhes do Usuário</h3>
            <div id="detailArea">
              <p style="color:var(--muted)">Selecione um usuário para ver detalhes e histórico.</p>
            </div>
          </div>
        </div>

        <footer>© NeuroLink Earth — Simulação Pro · Feito para testar ideias bilionárias</footer>
      </div>
    </div>
  </main>

  <script>
    /* ======= Storage helpers ======= */
    const STORAGE_KEY = "neurolink_users_v1";
    function saveAll() { localStorage.setItem(STORAGE_KEY, JSON.stringify(users)); }
    function loadAll() {
      try { const raw = localStorage.getItem(STORAGE_KEY); return raw ? JSON.parse(raw) : {}; } catch(e){return {}}
    }

    /* ======= Data model ======= */
    const users = loadAll(); // {id: {id,name,email,biometrics:{hr,sleep,stress},focus_history:[] } }
    let selectedId = null;

    /* ======= Utility: Predict Focus (improved) ======= */
    function predictFocus(bio) {
      // bio = {hr, sleep, stress}
      let foco = 100;
      const hr = Number(bio.hr || 70);
      const sleep = Number(bio.sleep || 7);
      const stress = Number(bio.stress || 10);

      // stress less aggressive
      foco -= stress * 0.6;

      // sleep penalty (ideal = 7-8)
      if (sleep < 7) foco -= (7 - sleep) * 6;
      if (sleep > 9) foco -= (sleep - 9) * 2; // oversleep slight penalty

      // heart rate penalty (optimum 60-90)
      if (hr < 60) foco -= (60 - hr) * 0.3;
      if (hr > 100) foco -= (hr - 100) * 0.5;

      // small random smoothing so charts vary visually
      foco = foco - Math.max(0, (Math.random() - 0.5) * 2);

      return Math.max(8, Math.min(100, Math.round(foco)));
    }

    /* ======= UI renderers ======= */
    const usersListEl = document.getElementById('usersList');
    const kpiUsersEl = document.getElementById('kpiUsers');
    const kpiFocusEl = document.getElementById('kpiFocus');
    const kpiSleepEl = document.getElementById('kpiSleep');
    const leaderboardEl = document.getElementById('leaderboard');
    const overviewCtx = document.getElementById('overviewChart').getContext('2d');
    const detailArea = document.getElementById('detailArea');

    function renderUsersList() {
      usersListEl.innerHTML = '';
      Object.keys(users).sort((a,b)=>a-b).forEach(id=>{
        const u = users[id];
        const item = document.createElement('div');
        item.className = 'user-item' + (id===selectedId ? ' selected' : '');
        item.onclick = ()=>{ selectUser(id); }
        item.innerHTML = `<div class="avatar">${u.name.slice(0,2).toUpperCase()}</div>
                          <div style="flex:1">
                            <div style="font-weight:700">${u.name}</div>
                            <div class="user-meta"><small>${u.email}</small></div>
                          </div>
                          <div style="text-align:right">
                            <div style="font-weight:700">${u.latest_focus ?? '—'}%</div>
                            <small style="color:var(--muted)">foco</small>
                          </div>`;
        usersListEl.appendChild(item);
      });
      updateKPIs();
    }

    function updateKPIs() {
      const ids = Object.keys(users);
      kpiUsersEl.textContent = ids.length;
      if (ids.length === 0) { kpiFocusEl.textContent = '—'; kpiSleepEl.textContent='—'; renderOverviewChart(); return; }
      let totalFocus=0, totalSleep=0, cnt=0;
      ids.forEach(id=>{
        const u = users[id];
        const last = u.focus_history && u.focus_history.length ? u.focus_history[u.focus_history.length-1].value : null;
        if (last !== null) { totalFocus += last; cnt++; }
        totalSleep += (u.biometria?.sleep ?? u.biometria?.sleep ?? 0);
      });
      kpiFocusEl.textContent = cnt ? Math.round(totalFocus/cnt) + '%' : '—';
      kpiSleepEl.textContent = ids.length ? (Math.round(totalSleep / ids.length * 10)/10) + 'h' : '—';
      renderOverviewChart();
      renderLeaderboard();
    }

    /* ======= Overview Chart (aggregated trends) ======= */
    let overviewChart = null;
    function renderOverviewChart(){
      const labels = [];
      const datasets = [];
      // Build aggregate average focus over time (last 12 points)
      const allSeries = [];
      Object.values(users).forEach(u=>{
        if (u.focus_history) {
          allSeries.push(u.focus_history.map(p=>p.value));
        }
      });
      // compute average per time index up to 12
      const maxLen = 12;
      const avg = [];
      for(let i=maxLen-1;i>=0;i--){
        let sum=0, cnt=0;
        allSeries.forEach(s=>{
          const val = s[s.length-1-i];
          if (typeof val==='number'){ sum+=val; cnt++; }
        });
        avg.push(cnt?Math.round(sum/cnt):null);
        labels.push( (i===0? 'Now': `-${i}`) );
      }
      // prepare dataset
      const final = avg.map(v=> v===null ? 0 : v);
      if (overviewChart) { overviewChart.data.labels = labels.reverse(); overviewChart.data.datasets[0].data = final.reverse(); overviewChart.update(); return; }
      overviewChart = new Chart(overviewCtx, {
        type:'line',
        data: {
          labels: labels.reverse(),
          datasets: [{
            label:'Média de Foco',
            data: final.reverse(),
            borderWidth:2,
            tension:0.3,
            pointRadius:2,
            fill:true,
            backgroundColor: 'rgba(6,182,212,0.08)',
            borderColor: 'rgba(6,182,212,0.9)'
          }]
        },
        options: {
          responsive:true,
          scales:{ y:{ min:0, max:100, ticks:{color:'#9fb7c4'}}, x:{ ticks:{color:'#9fb7c4'} } },
          plugins:{ legend:{display:false} }
        }
      });
    }

    function renderLeaderboard(){
      leaderboardEl.innerHTML = '';
      const arr = Object.keys(users).map(id => ({id, name:users[id].name, focus: users[id].latest_focus ?? 0}));
      arr.sort((a,b)=>b.focus - a.focus);
      arr.slice(0,6).forEach(item=>{
        const el = document.createElement('div');
        el.className = 'leader-item';
        el.innerHTML = `<div><strong>${item.name}</strong><div style="color:var(--muted);font-size:13px">ID ${item.id}</div></div><div style="font-weight:800">${item.focus}%</div>`;
        leaderboardEl.appendChild(el);
      });
    }

    function selectUser(id){
      selectedId = id;
      renderUsersList();
      showUserDetails(id);
    }

    function showUserDetails(id){
      const u = users[id];
      if (!u) { detailArea.innerHTML = '<p style="color:var(--muted)">Selecione um usuário</p>'; return; }
      // build detail card
      const last = u.focus_history && u.focus_history.length ? u.focus_history.slice(-12) : [];
      let sparkLabels = last.map((_,i)=>i+1);
      let sparkData = last.map(p=>p.value);
      detailArea.innerHTML = `
        <div style="display:flex;gap:12px;align-items:center">
          <div style="width:72px;height:72px;border-radius:12px;background:linear-gradient(135deg,#60a5fa,#34d399);display:flex;align-items:center;justify-content:center;font-weight:700;color:#042029;font-size:20px">${u.name.slice(0,2).toUpperCase()}</div>
          <div>
            <div style="font-weight:800;font-size:16px">${u.name}</div>
            <div style="color:var(--muted)">${u.email}</div>
            <div style="margin-top:6px;color:var(--muted)">Último foco: <strong>${u.latest_focus ?? '—'}%</strong></div>
          </div>
        </div>
        <div style="margin-top:12px">
          <canvas id="detailSpark" height="80"></canvas>
        </div>
        <div style="margin-top:12px;color:var(--muted);font-size:13px">
          <div>Batimentos: <strong>${u.biometria.hr ?? '—'}</strong></div>
          <div>Sono: <strong>${u.biometria.sleep ?? '—'} h</strong></div>
          <div>Estresse: <strong>${u.biometria.stress ?? '—'}</strong></div>
        </div>
      `;
      // draw sparkline
      setTimeout(()=>{ // ensure canvas is in DOM
        const ctx = document.getElementById('detailSpark').getContext('2d');
        new Chart(ctx, {
          type:'line',
          data:{ labels: sparkLabels, datasets:[ { data: sparkData, borderColor:'#8b5cf6', backgroundColor:'rgba(139,92,246,0.09)', tension:0.4, fill:true } ] },
          options:{ plugins:{legend:{display:false}}, scales:{x:{display:false},y:{display:true, min:0, max:100, ticks:{color:'#9fb7c4'}}} }
        });
      },20);
    }

    /* ======= Create / Update handlers ======= */
    document.getElementById('btnCreate').onclick = ()=>{
      const id = document.getElementById('inputId').value.trim();
      const name = document.getElementById('inputName').value.trim();
      const email = document.getElementById('inputEmail').value.trim();
      if (!id || !name || !email) return alert('Preencha ID, nome e email');
      if (users[id]) return alert('ID já existe');
      users[id] = {
        id, name, email,
        biometria: { hr:70, sleep:7, stress:10 },
        focus_history: [],
        latest_focus: null
      };
      saveAll(); renderUsersList();
      document.getElementById('inputId').value=''; document.getElementById('inputName').value=''; document.getElementById('inputEmail').value='';
    };

    document.getElementById('btnUpdate').onclick = ()=>{
      if (!selectedId) return alert('Selecione um usuário na lista');
      const hr = Number(document.getElementById('inputHR').value) || 70;
      const sleep = Number(document.getElementById('inputSleep').value) || 7;
      const stress = Number(document.getElementById('inputStress').value) || 10;
      const u = users[selectedId];
      u.biometria = { hr, sleep, stress };
      const newFocus = predictFocus({hr, sleep, stress});
      u.latest_focus = newFocus;
      if (!u.focus_history) u.focus_history = [];
      u.focus_history.push({ts:Date.now(), value:newFocus});
      if (u.focus_history.length > 48) u.focus_history.shift(); // keep last 48
      saveAll(); renderUsersList(); updateKPIs(); showUserDetails(selectedId);
      // clear inputs
      document.getElementById('inputHR').value=''; document.getElementById('inputSleep').value=''; document.getElementById('inputStress').value='';
    };

    /* ======= Export / Import ======= */
    document.getElementById('btnExportJSON').onclick = ()=>{
      const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(users));
      const dl = document.createElement('a');
      dl.setAttribute('href', dataStr);
      dl.setAttribute('download', 'neurolink_users.json');
      document.body.appendChild(dl); dl.click(); dl.remove();
    };
    document.getElementById('btnImportJSON').onclick = ()=> document.getElementById('fileInput').click();
    document.getElementById('fileInput').addEventListener('change', (ev)=>{
      const f = ev.target.files[0]; if(!f) return;
      const r = new FileReader();
      r.onload = ()=> {
        try {
          const imported = JSON.parse(r.result);
          Object.keys(imported).forEach(k=>users[k]=imported[k]);
          saveAll(); renderUsersList(); updateKPIs(); alert('Importado com sucesso');
        } catch(e){ alert('Arquivo inválido') }
      };
      r.readAsText(f);
    });

    /* ======= Export button (top) ======= */
    document.getElementById('exportBtn').onclick = ()=> document.getElementById('btnExportJSON').click();

    /* ======= Init ======= */
    (function init(){
      // if no users, seed with sample
      if (Object.keys(users).length===0){
        users['1'] = { id:'1', name:'Jardel', email:'jardel@example.com', biometria:{hr:72,sleep:7,stress:12}, focus_history:[], latest_focus:null };
        users['2'] = { id:'2', name:'Ana', email:'ana@example.com', biometria:{hr:64,sleep:6,stress:22}, focus_history:[], latest_focus:null };
        users['3'] = { id:'3', name:'Carlos', email:'carlos@example.com', biometria:{hr:78,sleep:5,stress:35}, focus_history:[], latest_focus:null };
      }
      // compute initial focuses (seed history)
      Object.keys(users).forEach(id=>{
        const u = users[id];
        const f = predictFocus(u.biometria || {});
        u.latest_focus = f;
        u.focus_history = u.focus_history || [];
        u.focus_history.push({ts:Date.now()-60000*3, value: Math.max(20, f-5)});
        u.focus_history.push({ts:Date.now()-60000*2, value: Math.max(20, f-2)});
        u.focus_history.push({ts:Date.now()-60000, value: f});
      });
      saveAll(); renderUsersList(); updateKPIs();
    })();
  </script>
</body>
</html>
