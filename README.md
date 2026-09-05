<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Balancete — controle financeiro mensal</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Source+Serif+4:opsz,wght@8..60,400;8..60,600;8..60,700&family=IBM+Plex+Mono:wght@400;500;600&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --paper: #F6F4EC;
    --paper-line: #DCE6D3;
    --paper-line-strong: #B9CBAB;
    --ink: #1E2A22;
    --ink-soft: #4B5A4F;
    --brick: #A63B2E;
    --brick-soft: #C97C6F;
    --brass: #8A6A22;
    --brass-soft: #E4D6A8;
    --rule: #C9C1AC;
    --white: #FFFFFF;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:var(--paper);
    color:var(--ink);
    font-family:'Inter',system-ui,sans-serif;
    font-size:15px;
    line-height:1.5;
    min-height:100vh;
  }
  .wrap{max-width:1080px;margin:0 auto;padding:28px 20px 80px;}

  /* Header / stamp */
  header.top{
    display:flex;
    justify-content:space-between;
    align-items:flex-end;
    border-bottom:3px double var(--ink);
    padding-bottom:14px;
    margin-bottom:22px;
    flex-wrap:wrap;
    gap:14px;
  }
  .brand h1{
    font-family:'Source Serif 4',serif;
    font-weight:700;
    font-size:30px;
    margin:0;
    letter-spacing:0.2px;
  }
  .brand p{margin:2px 0 0;color:var(--ink-soft);font-size:13px;}
  .month-stamp{
    display:flex;align-items:center;gap:10px;
    position:relative;
  }
  .month-nav-btn{
    background:none;border:1px solid var(--ink);color:var(--ink);
    width:30px;height:30px;border-radius:50%;
    font-size:16px;cursor:pointer;line-height:1;
    display:flex;align-items:center;justify-content:center;
    transition:background .15s ease;
  }
  .month-nav-btn:hover{background:var(--paper-line);}
  .stamp{
    font-family:'IBM Plex Mono',monospace;
    font-weight:600;
    font-size:15px;
    border:2px solid var(--brick);
    color:var(--brick);
    padding:6px 14px;
    border-radius:3px;
    transform:rotate(-2deg);
    cursor:pointer;
    background:var(--white);
    user-select:none;
    text-transform:uppercase;
    letter-spacing:0.5px;
  }
  .month-menu{
    position:absolute;top:44px;right:0;
    background:var(--white);
    border:1px solid var(--ink);
    box-shadow:3px 3px 0 var(--ink-soft);
    z-index:20;
    max-height:260px;overflow-y:auto;
    min-width:180px;
    display:none;
  }
  .month-menu.open{display:block;}
  .month-menu button{
    display:block;width:100%;text-align:left;
    padding:8px 12px;background:none;border:none;
    border-bottom:1px solid var(--paper-line);
    font-family:'IBM Plex Mono',monospace;font-size:13px;
    cursor:pointer;color:var(--ink);
  }
  .month-menu button:hover{background:var(--paper-line);}

  /* Layout */
  .grid{display:grid;grid-template-columns:1.5fr 1fr;gap:26px;}
  @media (max-width:820px){.grid{grid-template-columns:1fr;}}

  section.panel{margin-bottom:26px;}
  h2.section-title{
    font-family:'Source Serif 4',serif;
    font-size:16px;font-weight:600;
    margin:0 0 10px;
    padding-bottom:6px;
    border-bottom:1px solid var(--rule);
    color:var(--ink);
  }

  /* Balance hero */
  .balance-block{
    display:flex;justify-content:space-between;align-items:baseline;
    flex-wrap:wrap;gap:10px;
    margin-bottom:18px;
  }
  .balance-figure{
    font-family:'IBM Plex Mono',monospace;
    font-weight:600;
    font-size:44px;
    line-height:1;
    font-variant-numeric:tabular-nums;
  }
  .balance-figure.neg{color:var(--brick);}
  .balance-figure.pos{color:var(--ink);}
  .balance-sub{color:var(--ink-soft);font-size:13px;}
  .mini-totals{display:flex;gap:22px;font-family:'IBM Plex Mono',monospace;font-size:13px;}
  .mini-totals span.label{color:var(--ink-soft);display:block;font-family:'Inter',sans-serif;font-size:11px;}
  .mini-totals .in{color:var(--ink);}
  .mini-totals .out{color:var(--brick);}

  /* Ledger table */
  table.ledger{width:100%;border-collapse:collapse;font-size:14px;}
  table.ledger thead th{
    text-align:left;font-weight:500;color:var(--ink-soft);
    font-size:12px;padding:4px 6px;border-bottom:1px solid var(--ink);
  }
  table.ledger tbody tr{border-bottom:1px solid var(--paper-line);}
  table.ledger tbody tr:nth-child(even){background:var(--paper-line);}
  table.ledger td{padding:7px 6px;vertical-align:middle;}
  table.ledger td.num{
    font-family:'IBM Plex Mono',monospace;
    text-align:right;font-variant-numeric:tabular-nums;
    white-space:nowrap;
  }
  table.ledger td.neg{color:var(--brick);}
  table.ledger td.cat{color:var(--ink-soft);font-size:12px;}
  table.ledger td.del{text-align:center;width:24px;}
  .del-btn{
    background:none;border:none;color:var(--rule);cursor:pointer;font-size:14px;
  }
  .del-btn:hover{color:var(--brick);}
  .empty-row td{padding:18px 6px;color:var(--ink-soft);font-style:italic;text-align:center;}

  /* Add entry form */
  form.add-entry{
    display:grid;
    grid-template-columns: 110px 1fr 130px 110px 34px;
    gap:8px;
    margin-top:12px;
    align-items:end;
  }
  @media (max-width:600px){form.add-entry{grid-template-columns:1fr 1fr;}}
  form.add-entry label{display:block;font-size:11px;color:var(--ink-soft);margin-bottom:3px;}
  form.add-entry input, form.add-entry select{
    width:100%;padding:6px 7px;border:1px solid var(--rule);background:var(--white);
    font-family:'Inter',sans-serif;font-size:13px;color:var(--ink);border-radius:2px;
  }
  form.add-entry input:focus, form.add-entry select:focus{outline:2px solid var(--brass);border-color:var(--brass);}
  .type-toggle{display:flex;border:1px solid var(--rule);border-radius:2px;overflow:hidden;}
  .type-toggle button{
    flex:1;padding:6px 4px;font-size:12px;background:var(--white);border:none;cursor:pointer;color:var(--ink-soft);
  }
  .type-toggle button.active-out{background:var(--brick);color:var(--white);}
  .type-toggle button.active-in{background:var(--ink);color:var(--white);}
  .add-btn{
    background:var(--ink);color:var(--white);border:none;border-radius:2px;
    height:31px;font-size:18px;cursor:pointer;line-height:1;
  }
  .add-btn:hover{background:var(--brass);}

  /* Category bars */
  .cat-bar-row{margin-bottom:10px;}
  .cat-bar-head{display:flex;justify-content:space-between;font-size:12.5px;margin-bottom:3px;}
  .cat-bar-head .amt{font-family:'IBM Plex Mono',monospace;font-variant-numeric:tabular-nums;}
  .cat-bar-track{height:7px;background:var(--paper-line);border-radius:1px;overflow:hidden;}
  .cat-bar-fill{height:100%;background:var(--ink-soft);}
  .no-data{color:var(--ink-soft);font-style:italic;font-size:13px;}

  /* Goals */
  .goal-row{margin-bottom:13px;}
  .goal-head{display:flex;justify-content:space-between;align-items:baseline;font-size:13px;margin-bottom:4px;}
  .goal-head .cat-name{font-weight:500;}
  .goal-head .goal-nums{font-family:'IBM Plex Mono',monospace;font-size:12px;color:var(--ink-soft);}
  .goal-track{height:5px;background:var(--paper-line);position:relative;}
  .goal-fill{height:100%;background:var(--brass);}
  .goal-fill.over{background:var(--brick);}
  .goal-empty{color:var(--ink-soft);font-size:12.5px;font-style:italic;}

  .goal-setup{
    display:flex;gap:8px;margin-top:14px;flex-wrap:wrap;align-items:end;
    border-top:1px dashed var(--rule);padding-top:12px;
  }
  .goal-setup label{display:block;font-size:11px;color:var(--ink-soft);margin-bottom:3px;}
  .goal-setup select, .goal-setup input{
    padding:6px 7px;border:1px solid var(--rule);background:var(--white);font-size:13px;border-radius:2px;
  }
  .goal-setup button{
    background:var(--ink);color:var(--white);border:none;padding:7px 12px;border-radius:2px;cursor:pointer;font-size:12px;
  }
  .goal-setup button:hover{background:var(--brass);}

  /* Categories manager */
  .cat-manager{display:flex;gap:8px;flex-wrap:wrap;align-items:end;}
  .cat-manager input, .cat-manager select{
    padding:6px 7px;border:1px solid var(--rule);font-size:13px;border-radius:2px;
  }
  .cat-manager button{
    background:var(--white);border:1px solid var(--ink);color:var(--ink);padding:6px 12px;border-radius:2px;cursor:pointer;font-size:12px;
  }
  .cat-manager button:hover{background:var(--ink);color:var(--white);}
  .cat-chip-list{display:flex;flex-wrap:wrap;gap:6px;margin-top:10px;}
  .cat-chip{
    font-size:11.5px;border:1px solid var(--rule);border-radius:11px;padding:3px 10px;
    color:var(--ink-soft);display:flex;align-items:center;gap:5px;
  }
  .cat-chip.despesa{border-color:var(--brick-soft);}
  .cat-chip.receita{border-color:var(--paper-line-strong);}

  footer.utility{
    margin-top:34px;border-top:1px solid var(--rule);padding-top:14px;
    display:flex;justify-content:space-between;flex-wrap:wrap;gap:10px;
    font-size:12px;color:var(--ink-soft);
  }
  footer.utility button{
    background:none;border:1px solid var(--rule);color:var(--ink-soft);
    padding:5px 10px;border-radius:2px;cursor:pointer;font-size:12px;
  }
  footer.utility button:hover{border-color:var(--ink);color:var(--ink);}
  #importFile{display:none;}
</style>
</head>
<body>
<div class="wrap">

  <header class="top">
    <div class="brand">
      <h1>Balancete</h1>
      <p>Controle financeiro mensal</p>
    </div>
    <div class="month-stamp">
      <button class="month-nav-btn" id="prevMonth" aria-label="Mês anterior">‹</button>
      <div class="stamp" id="monthStamp">— —</div>
      <button class="month-nav-btn" id="nextMonth" aria-label="Próximo mês">›</button>
      <div class="month-menu" id="monthMenu"></div>
    </div>
  </header>

  <div class="grid">
    <div>
      <section class="panel">
        <div class="balance-block">
          <div>
            <div class="balance-figure" id="balanceFigure">R$ 0,00</div>
            <div class="balance-sub">saldo do mês</div>
          </div>
          <div class="mini-totals">
            <div><span class="label">receitas</span><span class="in" id="totalIn">R$ 0,00</span></div>
            <div><span class="label">despesas</span><span class="out" id="totalOut">R$ 0,00</span></div>
          </div>
        </div>
      </section>

      <section class="panel">
        <h2 class="section-title">Lançamentos</h2>
        <table class="ledger">
          <thead>
            <tr><th>Data</th><th>Descrição</th><th>Categoria</th><th style="text-align:right;">Valor</th><th style="text-align:right;">Saldo</th><th></th></tr>
          </thead>
          <tbody id="ledgerBody"></tbody>
        </table>

        <form class="add-entry" id="addEntryForm">
          <div>
            <label>Data</label>
            <input type="date" id="fData" required>
          </div>
          <div>
            <label>Descrição</label>
            <input type="text" id="fDesc" placeholder="ex.: mercado da semana" required>
          </div>
          <div>
            <label>Categoria</label>
            <select id="fCat" required></select>
          </div>
          <div>
            <label>Valor / tipo</label>
            <div style="display:flex;gap:6px;">
              <input type="text" inputmode="decimal" id="fValor" placeholder="0,00" required style="flex:1;">
            </div>
          </div>
          <button type="submit" class="add-btn" title="Adicionar lançamento">+</button>
          <div style="grid-column:1 / -1;">
            <div class="type-toggle" id="typeToggle">
              <button type="button" data-type="despesa" class="active-out">despesa</button>
              <button type="button" data-type="receita">receita</button>
            </div>
          </div>
        </form>
      </section>

      <section class="panel">
        <h2 class="section-title">Categorias</h2>
        <div class="cat-manager">
          <div>
            <label style="display:block;font-size:11px;color:var(--ink-soft);margin-bottom:3px;">Nova categoria</label>
            <input type="text" id="newCatName" placeholder="ex.: assinaturas">
          </div>
          <select id="newCatType">
            <option value="despesa">despesa</option>
            <option value="receita">receita</option>
          </select>
          <button type="button" id="addCatBtn">adicionar</button>
        </div>
        <div class="cat-chip-list" id="catChipList"></div>
      </section>
    </div>

    <div>
      <section class="panel">
        <h2 class="section-title">Gastos por categoria</h2>
        <div id="catBars"></div>
      </section>

      <section class="panel">
        <h2 class="section-title">Metas do mês</h2>
        <div id="goalsList"></div>
        <div class="goal-setup">
          <div>
            <label>Categoria</label>
            <select id="goalCat"></select>
          </div>
          <div>
            <label>Meta (R$)</label>
            <input type="text" inputmode="decimal" id="goalValor" placeholder="0,00" style="width:100px;">
          </div>
          <button type="button" id="setGoalBtn">salvar meta</button>
        </div>
      </section>
    </div>
  </div>

  <footer class="utility">
    <span>Os dados ficam salvos neste navegador.</span>
    <span>
      <button id="exportBtn">exportar backup</button>
      <button id="importBtn">importar backup</button>
      <input type="file" id="importFile" accept="application/json">
    </span>
  </footer>

</div>

<script>
(function(){
  const STORAGE_KEY = 'balancete_data_v1';
  const MESES = ['janeiro','fevereiro','março','abril','maio','junho','julho','agosto','setembro','outubro','novembro','dezembro'];

  function defaultData(){
    return {
      categories: [
        {id:'c1', nome:'moradia', tipo:'despesa', meta:null},
        {id:'c2', nome:'mercado', tipo:'despesa', meta:null},
        {id:'c3', nome:'transporte', tipo:'despesa', meta:null},
        {id:'c4', nome:'lazer', tipo:'despesa', meta:null},
        {id:'c5', nome:'saúde', tipo:'despesa', meta:null},
        {id:'c6', nome:'outros', tipo:'despesa', meta:null},
        {id:'c7', nome:'salário', tipo:'receita', meta:null},
        {id:'c8', nome:'extra', tipo:'receita', meta:null}
      ],
      months: {}
    };
  }

  function loadData(){
    try{
      const raw = localStorage.getItem(STORAGE_KEY);
      if(!raw) return defaultData();
      const parsed = JSON.parse(raw);
      if(!parsed.categories || !parsed.months) return defaultData();
      return parsed;
    }catch(e){ return defaultData(); }
  }

  function saveData(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(data)); }

  let data = loadData();

  function monthKey(d){ return d.getFullYear()+'-'+String(d.getMonth()+1).padStart(2,'0'); }
  function keyToLabel(key){
    const [y,m] = key.split('-').map(Number);
    return MESES[m-1] + ' ' + y;
  }
  function ensureMonth(key){
    if(!data.months[key]) data.months[key] = {entries:[]};
    return data.months[key];
  }

  let currentDate = new Date();
  let currentKey = monthKey(currentDate);
  ensureMonth(currentKey);

  function fmt(v){
    return 'R$ ' + Math.abs(v).toLocaleString('pt-BR',{minimumFractionDigits:2, maximumFractionDigits:2});
  }
  function parseValor(str){
    if(!str) return NaN;
    const cleaned = str.replace(/\s/g,'').replace(/\./g,'').replace(',','.');
    return parseFloat(cleaned);
  }

  function catById(id){ return data.categories.find(c=>c.id===id); }

  function populateCategorySelects(){
    const type = document.querySelector('#typeToggle button.active-out, #typeToggle button.active-in').dataset.type;
    const fCat = document.getElementById('fCat');
    fCat.innerHTML = '';
    data.categories.filter(c=>c.tipo===type).forEach(c=>{
      const opt = document.createElement('option');
      opt.value = c.id; opt.textContent = c.nome;
      fCat.appendChild(opt);
    });
    const goalCat = document.getElementById('goalCat');
    goalCat.innerHTML = '';
    data.categories.filter(c=>c.tipo==='despesa').forEach(c=>{
      const opt = document.createElement('option');
      opt.value = c.id; opt.textContent = c.nome;
      goalCat.appendChild(opt);
    });
  }

  function renderMonthStamp(){
    document.getElementById('monthStamp').textContent = keyToLabel(currentKey);
  }

  function renderMonthMenu(){
    const menu = document.getElementById('monthMenu');
    const keys = Object.keys(data.months).sort().reverse();
    menu.innerHTML = '';
    if(keys.length===0){
      const b = document.createElement('button');
      b.textContent = 'nenhum mês registrado ainda';
      b.disabled = true;
      menu.appendChild(b);
    }
    keys.forEach(k=>{
      const b = document.createElement('button');
      b.textContent = keyToLabel(k);
      b.addEventListener('click', ()=>{ currentKey = k; menu.classList.remove('open'); renderAll(); });
      menu.appendChild(b);
    });
  }

  function renderLedger(){
    const month = ensureMonth(currentKey);
    const body = document.getElementById('ledgerBody');
    body.innerHTML = '';
    if(month.entries.length===0){
      body.innerHTML = '<tr class="empty-row"><td colspan="6">nenhum lançamento neste mês</td></tr>';
    }else{
      const sorted = [...month.entries].sort((a,b)=> a.data.localeCompare(b.data));
      let running = 0;
      sorted.forEach(e=>{
        running += e.tipo==='receita' ? e.valor : -e.valor;
        const tr = document.createElement('tr');
        const cat = catById(e.categoriaId);
        const [y,m,d] = e.data.split('-');
        tr.innerHTML = `
          <td>${d}/${m}</td>
          <td>${e.descricao}</td>
          <td class="cat">${cat ? cat.nome : '—'}</td>
          <td class="num ${e.tipo==='despesa'?'neg':''}">${e.tipo==='despesa'?'-':'+'} ${fmt(e.valor)}</td>
          <td class="num ${running<0?'neg':''}">${fmt(running)}</td>
          <td class="del"><button class="del-btn" data-id="${e.id}" title="excluir">✕</button></td>
        `;
        body.appendChild(tr);
      });
    }
    body.querySelectorAll('.del-btn').forEach(btn=>{
      btn.addEventListener('click', ()=>{
        const id = btn.dataset.id;
        month.entries = month.entries.filter(e=>e.id!==id);
        saveData(); renderAll();
      });
    });
  }

  function renderSummary(){
    const month = ensureMonth(currentKey);
    let totalIn=0, totalOut=0;
    month.entries.forEach(e=>{
      if(e.tipo==='receita') totalIn += e.valor; else totalOut += e.valor;
    });
    const saldo = totalIn - totalOut;
    const fig = document.getElementById('balanceFigure');
    fig.textContent = (saldo<0?'-':'') + fmt(saldo);
    fig.className = 'balance-figure ' + (saldo<0?'neg':'pos');
    document.getElementById('totalIn').textContent = fmt(totalIn);
    document.getElementById('totalOut').textContent = fmt(totalOut);
  }

  function renderCatBars(){
    const month = ensureMonth(currentKey);
    const totals = {};
    month.entries.filter(e=>e.tipo==='despesa').forEach(e=>{
      totals[e.categoriaId] = (totals[e.categoriaId]||0) + e.valor;
    });
    const container = document.getElementById('catBars');
    const entries = Object.entries(totals).sort((a,b)=>b[1]-a[1]);
    container.innerHTML = '';
    if(entries.length===0){
      container.innerHTML = '<div class="no-data">sem despesas registradas neste mês</div>';
      return;
    }
    const max = entries[0][1];
    entries.forEach(([catId, val])=>{
      const cat = catById(catId);
      const row = document.createElement('div');
      row.className = 'cat-bar-row';
      row.innerHTML = `
        <div class="cat-bar-head"><span>${cat?cat.nome:'—'}</span><span class="amt">${fmt(val)}</span></div>
        <div class="cat-bar-track"><div class="cat-bar-fill" style="width:${(val/max*100).toFixed(1)}%"></div></div>
      `;
      container.appendChild(row);
    });
  }

  function renderGoals(){
    const month = ensureMonth(currentKey);
    const totals = {};
    month.entries.filter(e=>e.tipo==='despesa').forEach(e=>{
      totals[e.categoriaId] = (totals[e.categoriaId]||0) + e.valor;
    });
    const list = document.getElementById('goalsList');
    const withGoals = data.categories.filter(c=>c.tipo==='despesa' && c.meta!=null);
    list.innerHTML = '';
    if(withGoals.length===0){
      list.innerHTML = '<div class="goal-empty">nenhuma meta definida ainda</div>';
      return;
    }
    withGoals.forEach(c=>{
      const gasto = totals[c.id]||0;
      const pct = Math.min(100, (gasto/c.meta*100));
      const over = gasto > c.meta;
      const row = document.createElement('div');
      row.className = 'goal-row';
      row.innerHTML = `
        <div class="goal-head">
          <span class="cat-name">${c.nome}</span>
          <span class="goal-nums">${fmt(gasto)} / ${fmt(c.meta)}</span>
        </div>
        <div class="goal-track"><div class="goal-fill ${over?'over':''}" style="width:${pct}%"></div></div>
      `;
      list.appendChild(row);
    });
  }

  function renderCatChips(){
    const list = document.getElementById('catChipList');
    list.innerHTML = '';
    data.categories.forEach(c=>{
      const chip = document.createElement('div');
      chip.className = 'cat-chip ' + c.tipo;
      chip.textContent = c.nome + (c.meta!=null ? ` · meta ${fmt(c.meta)}` : '');
      list.appendChild(chip);
    });
  }

  function renderAll(){
    renderMonthStamp();
    renderMonthMenu();
    populateCategorySelects();
    renderLedger();
    renderSummary();
    renderCatBars();
    renderGoals();
    renderCatChips();
  }

  // --- Events ---
  document.getElementById('prevMonth').addEventListener('click', ()=>{
    const [y,m] = currentKey.split('-').map(Number);
    const d = new Date(y, m-2, 1);
    currentKey = monthKey(d);
    ensureMonth(currentKey);
    renderAll();
  });
  document.getElementById('nextMonth').addEventListener('click', ()=>{
    const [y,m] = currentKey.split('-').map(Number);
    const d = new Date(y, m, 1);
    currentKey = monthKey(d);
    ensureMonth(currentKey);
    renderAll();
  });
  document.getElementById('monthStamp').addEventListener('click', ()=>{
    document.getElementById('monthMenu').classList.toggle('open');
  });
  document.addEventListener('click', (e)=>{
    const menu = document.getElementById('monthMenu');
    const stamp = document.getElementById('monthStamp');
    if(!menu.contains(e.target) && e.target!==stamp) menu.classList.remove('open');
  });

  document.getElementById('typeToggle').addEventListener('click', (e)=>{
    const btn = e.target.closest('button');
    if(!btn) return;
    document.querySelectorAll('#typeToggle button').forEach(b=>b.className='');
    btn.className = btn.dataset.type==='despesa' ? 'active-out' : 'active-in';
    populateCategorySelects();
  });

  document.getElementById('addEntryForm').addEventListener('submit', (e)=>{
    e.preventDefault();
    const type = document.querySelector('#typeToggle button.active-out, #typeToggle button.active-in').dataset.type;
    const valor = parseValor(document.getElementById('fValor').value);
    if(isNaN(valor) || valor<=0){ alert('Informe um valor válido.'); return; }
    const entry = {
      id: 'e'+Date.now()+Math.random().toString(36).slice(2,7),
      data: document.getElementById('fData').value,
      descricao: document.getElementById('fDesc').value.trim() || '(sem descrição)',
      categoriaId: document.getElementById('fCat').value,
      tipo: type,
      valor: valor
    };
    const entryMonthKey = entry.data.slice(0,7);
    ensureMonth(entryMonthKey).entries.push(entry);
    saveData();
    if(entryMonthKey !== currentKey){ currentKey = entryMonthKey; }
    e.target.reset();
    document.getElementById('fData').value = '';
    renderAll();
  });

  document.getElementById('addCatBtn').addEventListener('click', ()=>{
    const nameInput = document.getElementById('newCatName');
    const name = nameInput.value.trim();
    if(!name) return;
    const tipo = document.getElementById('newCatType').value;
    data.categories.push({id:'c'+Date.now(), nome:name, tipo:tipo, meta:null});
    saveData();
    nameInput.value = '';
    renderAll();
  });

  document.getElementById('setGoalBtn').addEventListener('click', ()=>{
    const catId = document.getElementById('goalCat').value;
    const valor = parseValor(document.getElementById('goalValor').value);
    if(!catId || isNaN(valor) || valor<=0){ alert('Selecione uma categoria e informe uma meta válida.'); return; }
    const cat = catById(catId);
    cat.meta = valor;
    saveData();
    document.getElementById('goalValor').value = '';
    renderAll();
  });

  document.getElementById('exportBtn').addEventListener('click', ()=>{
    const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url; a.download = 'balancete-backup.json';
    a.click();
    URL.revokeObjectURL(url);
  });

  document.getElementById('importBtn').addEventListener('click', ()=>{
    document.getElementById('importFile').click();
  });
  document.getElementById('importFile').addEventListener('change', (e)=>{
    const file = e.target.files[0];
    if(!file) return;
    const reader = new FileReader();
    reader.onload = (ev)=>{
      try{
        const parsed = JSON.parse(ev.target.result);
        if(!parsed.categories || !parsed.months) throw new Error('formato inválido');
        data = parsed;
        saveData();
        renderAll();
        alert('Backup importado com sucesso.');
      }catch(err){
        alert('Não foi possível importar este arquivo.');
      }
    };
    reader.readAsText(file);
  });

  // init
  document.getElementById('fData').value = new Date().toISOString().slice(0,10);
  renderAll();
})();
</script>
</body>
</html>
