# project-wild
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>STREAM/LINE — Netflix Content Intelligence</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Space+Mono:wght@400;700&family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
  :root{
    --ink:#0a0a0c;
    --panel:#141417;
    --panel-raised:#1b1b1f;
    --line:#2a2a2f;
    --paper:#eee9e2;
    --red:#e0202c;
    --red-dim:#7a1017;
    --gold:#d4af37;
    --muted:#8a8a92;
    --grain-opacity: 0.05;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:var(--ink);
    color:var(--paper);
    font-family:'Inter',sans-serif;
    min-height:100vh;
    position:relative;
    overflow-x:hidden;
  }
  /* film grain */
  body::before{
    content:"";
    position:fixed; inset:0;
    pointer-events:none;
    z-index:9999;
    opacity:var(--grain-opacity);
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
    mix-blend-mode:overlay;
  }
  .eyebrow{
    font-family:'Space Mono',monospace;
    font-size:11px;
    letter-spacing:.25em;
    text-transform:uppercase;
    color:var(--red);
  }
  h1,h2,h3{font-family:'Bebas Neue',sans-serif; letter-spacing:.02em; margin:0;}
 
  /* ---------- HERO / MARQUEE ---------- */
  .hero{
    padding:64px 6vw 40px;
    border-bottom:1px solid var(--line);
    position:relative;
  }
  .hero-top{
    display:flex; justify-content:space-between; align-items:flex-start; gap:24px; flex-wrap:wrap;
  }
  .marquee-wrap{overflow:hidden; border-top:2px solid var(--red); border-bottom:2px solid var(--red); padding:14px 0; margin:28px 0 8px;}
  .marquee-track{
    display:flex; white-space:nowrap; width:max-content;
    animation:scroll-left 26s linear infinite;
    font-family:'Bebas Neue',sans-serif;
    font-size:clamp(28px,5vw,54px);
    color:var(--ink);
  }
  .marquee-track span{
    -webkit-text-stroke:1.5px var(--paper);
    color:var(--ink);
    padding:0 28px;
    display:inline-flex; align-items:center; gap:18px;
  }
  .marquee-track span::after{content:"●"; color:var(--red); font-size:14px; -webkit-text-stroke:0;}
  @keyframes scroll-left{ from{transform:translateX(0);} to{transform:translateX(-50%);} }
 
  h1.title{
    font-size:clamp(44px,8vw,104px);
    line-height:.92;
    color:var(--paper);
  }
  h1.title .red{color:var(--red);}
  .hero-sub{
    max-width:520px;
    color:var(--muted);
    font-size:15px;
    line-height:1.6;
    margin-top:14px;
  }
  .stat-strip{
    display:grid; grid-template-columns:repeat(4,minmax(0,1fr));
    gap:1px; background:var(--line);
    margin-top:36px; border:1px solid var(--line);
  }
  .stat-cell{
    background:var(--panel); padding:22px 20px;
  }
  .stat-num{
    font-family:'Space Mono',monospace;
    font-weight:700;
    font-size:clamp(24px,3vw,36px);
    color:var(--gold);
  }
  .stat-label{
    font-size:11px; text-transform:uppercase; letter-spacing:.1em; color:var(--muted); margin-top:6px;
  }
 
  /* ---------- FILM STRIP DIVIDER ---------- */
  .filmstrip{
    height:34px;
    background:repeating-linear-gradient(90deg, var(--ink) 0 18px, var(--panel) 18px 20px);
    position:relative;
    display:flex; align-items:center;
    border-top:1px solid var(--line); border-bottom:1px solid var(--line);
  }
  .filmstrip::before, .filmstrip::after{
    content:"";
    position:absolute; left:0; right:0;
    height:10px;
    background-image:radial-gradient(circle, var(--ink) 3.5px, transparent 3.6px);
    background-size:26px 10px;
    background-repeat:repeat-x;
  }
  .filmstrip::before{top:4px;}
  .filmstrip::after{bottom:4px;}
 
  /* ---------- SECTIONS ---------- */
  .section{padding:56px 6vw;}
  .section-head{
    display:flex; align-items:baseline; gap:16px; margin-bottom:28px; flex-wrap:wrap;
  }
  .section-head h2{font-size:clamp(28px,4vw,42px);}
  .section-head .eyebrow{margin-left:auto;}
 
  .grid-2{display:grid; grid-template-columns:1.3fr .9fr; gap:20px;}
  .grid-3{display:grid; grid-template-columns:repeat(3,1fr); gap:20px;}
  @media (max-width:900px){ .grid-2,.grid-3{grid-template-columns:1fr;} }
 
  .cell{
    background:var(--panel);
    border:1px solid var(--line);
    border-radius:2px;
    padding:22px;
    position:relative;
  }
  .cell::before{
    content:attr(data-tag);
    position:absolute; top:-1px; left:18px;
    background:var(--red);
    color:var(--ink);
    font-family:'Space Mono',monospace;
    font-size:10px; font-weight:700;
    letter-spacing:.1em;
    padding:3px 8px;
    transform:translateY(-100%);
  }
  .cell h3{font-size:22px; color:var(--paper); margin-bottom:2px;}
  .cell .cell-sub{color:var(--muted); font-size:12px; margin-bottom:14px;}
  .chart-box{position:relative; height:280px;}
  .chart-box.tall{height:340px;}
 
  /* ---------- FILTER BAR ---------- */
  .filter-bar{
    display:flex; gap:10px; flex-wrap:wrap; margin-bottom:24px;
    background:var(--panel-raised); border:1px solid var(--line); padding:14px; border-radius:2px;
  }
  .filter-bar select, .filter-bar input{
    background:var(--ink); color:var(--paper);
    border:1px solid var(--line);
    font-family:'Inter',sans-serif; font-size:13px;
    padding:9px 12px; border-radius:2px;
  }
  .filter-bar input{flex:1; min-width:160px;}
  .filter-bar select:focus, .filter-bar input:focus{outline:2px solid var(--red); outline-offset:1px;}
  .chip{
    font-family:'Space Mono',monospace; font-size:11px;
    color:var(--gold); border:1px solid var(--gold);
    padding:8px 12px; border-radius:2px; cursor:pointer;
    background:transparent;
  }
  .chip:hover{background:var(--gold); color:var(--ink);}
 
  /* ---------- GUIDE TABLE ---------- */
  .guide{width:100%; border-collapse:collapse; font-size:13px;}
  .guide thead th{
    text-align:left; font-family:'Space Mono',monospace; text-transform:uppercase;
    font-size:10px; letter-spacing:.08em; color:var(--muted);
    border-bottom:1px solid var(--line); padding:10px 12px;
  }
  .guide tbody td{padding:12px; border-bottom:1px solid var(--line); vertical-align:top;}
  .guide tbody tr{cursor:default;}
  .guide tbody tr:hover{background:var(--panel-raised);}
  .badge{
    display:inline-block; font-family:'Space Mono',monospace; font-size:10px;
    padding:2px 7px; border-radius:2px; border:1px solid currentColor;
  }
  .badge.movie{color:#5ba8ff;}
  .badge.tv{color:var(--red);}
  .rating-pill{
    font-family:'Space Mono',monospace; font-size:10px; color:var(--gold);
    border:1px solid var(--gold); padding:2px 6px; border-radius:20px;
  }
  .guide-title{color:var(--paper); font-weight:600;}
  .guide-genres{color:var(--muted); font-size:11px;}
  .table-wrap{max-height:520px; overflow-y:auto; border:1px solid var(--line);}
  .table-wrap::-webkit-scrollbar{width:8px;}
  .table-wrap::-webkit-scrollbar-thumb{background:var(--red-dim);}
  .result-count{font-family:'Space Mono',monospace; font-size:11px; color:var(--muted); margin-bottom:10px;}
 
  footer{
    padding:36px 6vw 60px; color:var(--muted); font-size:12px;
    border-top:1px solid var(--line);
    display:flex; justify-content:space-between; flex-wrap:wrap; gap:8px;
  }
  a{color:var(--gold);}
 
  @media (prefers-reduced-motion: reduce){
    .marquee-track{animation:none;}
  }
</style>
</head>
<body>
 
<section class="hero">
  <div class="hero-top">
    <div>
      <div class="eyebrow">DATASET · 2008 – 2024 · SYNTHETIC SAMPLE FOR DEMO</div>
      <h1 class="title">STREAM<span class="red">/</span>LINE</h1>
      <p class="hero-sub">A control room for a streaming catalog: what genres dominate, which countries supply the most titles, how the Movie-to-Series balance has shifted year over year, and a full searchable programming guide underneath it all.</p>
    </div>
  </div>
 
  <div class="marquee-wrap">
    <div class="marquee-track" id="marqueeTrack"></div>
  </div>
 
  <div class="stat-strip" id="statStrip"></div>
</section>
 
<div class="filmstrip"></div>
 
<section class="section">
  <div class="section-head">
    <h2>Catalog growth</h2>
    <div class="eyebrow">Titles added by year</div>
  </div>
  <div class="grid-2">
    <div class="cell" data-tag="Fig. 01">
      <h3>Movies vs. Series, by release year</h3>
      <div class="cell-sub">Stacked additions — hover a bar for the exact split</div>
      <div class="chart-box tall"><canvas id="growthChart"></canvas></div>
    </div>
    <div class="cell" data-tag="Fig. 02">
      <h3>Runtime distribution</h3>
      <div class="cell-sub">Feature film length, in minutes</div>
      <div class="chart-box tall"><canvas id="runtimeChart"></canvas></div>
    </div>
  </div>
</section>
 
<div class="filmstrip"></div>
 
<section class="section">
  <div class="section-head">
    <h2>Genre &amp; geography</h2>
    <div class="eyebrow">What gets made, and where</div>
  </div>
  <div class="grid-3">
    <div class="cell" data-tag="Fig. 03">
      <h3>Top genres</h3>
      <div class="cell-sub">Share of catalog</div>
      <div class="chart-box"><canvas id="genreChart"></canvas></div>
    </div>
    <div class="cell" data-tag="Fig. 04">
      <h3>Top producing countries</h3>
      <div class="cell-sub">Titles by country of origin</div>
      <div class="chart-box"><canvas id="countryChart"></canvas></div>
    </div>
    <div class="cell" data-tag="Fig. 05">
      <h3>Maturity ratings</h3>
      <div class="cell-sub">Certificate mix across the catalog</div>
      <div class="chart-box"><canvas id="ratingChart"></canvas></div>
    </div>
  </div>
</section>
 
<div class="filmstrip"></div>
 
<section class="section">
  <div class="section-head">
    <h2>Programming guide</h2>
    <div class="eyebrow" id="guideCount"></div>
  </div>
 
  <div class="filter-bar">
    <input type="text" id="searchInput" placeholder="Search by title…">
    <select id="typeFilter"><option value="">All types</option></select>
    <select id="genreFilter"><option value="">All genres</option></select>
    <select id="countryFilter"><option value="">All countries</option></select>
    <select id="ratingFilter"><option value="">All ratings</option></select>
    <button class="chip" id="resetFilters">RESET ⟲</button>
  </div>
 
  <div class="result-count" id="resultCount"></div>
  <div class="table-wrap">
    <table class="guide">
      <thead>
        <tr><th>Title</th><th>Type</th><th>Year</th><th>Country</th><th>Genre</th><th>Rating</th><th>Run</th></tr>
      </thead>
      <tbody id="guideBody"></tbody>
    </table>
  </div>
</section>
 
<footer>
  <span>STREAM/LINE — a content-intelligence demo</span>
  <span>Data synthesized for illustration, patterned on real catalog composition</span>
</footer>
 
<script>
/* ============================================================
   SYNTHETIC DATASET GENERATOR
   Distributions are patterned after well-known real-world
   streaming catalog composition (genre mix, country skew,
   movie/series ratio, rating spread) — not a real dataset.
   ============================================================ */
(function(){
  let seed = 42;
  function rand(){ seed = (seed*1103515245+12345)%2147483648; return seed/2147483648; }
  function pick(arr){ return arr[Math.floor(rand()*arr.length)]; }
  function weightedPick(pairs){
    const total = pairs.reduce((s,p)=>s+p[1],0);
    let r = rand()*total;
    for(const [val,w] of pairs){ if((r-=w)<=0) return val; }
    return pairs[0][0];
  }
 
  const GENRES = [
    ['Drama',22],['Comedy',16],['Documentary',11],['Action',10],['Thriller',9],
    ['Romance',8],['Horror',6],['Crime',6],['Sci-Fi',5],['Kids & Family',4],
    ['Anime',4],['Reality TV',3]
  ];
  const COUNTRIES = [
    ['United States',34],['India',13],['United Kingdom',9],['South Korea',7],
    ['Japan',6],['France',6],['Canada',5],['Spain',5],['Germany',4],
    ['Australia',3],['Brazil',3],['Mexico',3],['Nigeria',2]
  ];
  const RATINGS_MOVIE = [['R',26],['PG-13',22],['TV-MA',20],['PG',12],['TV-14',10],['NR',6],['G',4]];
  const RATINGS_TV    = [['TV-MA',34],['TV-14',28],['TV-PG',16],['TV-Y7',10],['TV-G',6],['NR',6]];
 
  const ADJ = ['Silent','Last','Broken','Golden','Midnight','Hidden','Crimson','Lost','Endless','Fractured','Quiet','Distant','Electric','Forgotten','Neon','Iron','Velvet','Paper','Radiant','Static'];
  const NOUN = ['Horizon','Kingdom','Signal','Harbor','Machine','Garden','Empire','Echo','Current','Vow','District','Frontier','Legacy','Static','Tide','Wire','Season','Verdict','Orbit','Shelter'];
  const CONNECT = ['of the','and the','in the','beyond the','after the','before the'];
  const SUFFIX = ['City','North','Season One','Rising','Chronicles','Redux','Protocol','Diaries','Uprising','Revisited'];
 
  function genTitle(){
    const style = rand();
    if(style < 0.4) return `${pick(ADJ)} ${pick(NOUN)}`;
    if(style < 0.7) return `The ${pick(NOUN)} ${pick(CONNECT)} ${pick(NOUN)}`;
    return `${pick(NOUN)}: ${pick(SUFFIX)}`;
  }
 
  const N = 480;
  const rows = [];
  for(let i=0;i<N;i++){
    const type = weightedPick([['Movie',68],['TV Show',32]]);
    // catalog additions skew toward more recent years, peaking ~2019
    const yearWeights = [];
    for(let y=2008;y<=2024;y++){
      let w;
      if(y<2015) w = 2 + (y-2008)*1.2;
      else if(y<=2020) w = 10 + (y-2015)*6;
      else w = Math.max(6, 40 - (y-2020)*6);
      yearWeights.push([y,w]);
    }
    const year = weightedPick(yearWeights);
    const genre = weightedPick(GENRES);
    const country = weightedPick(COUNTRIES);
    const rating = weightedPick(type==='Movie'?RATINGS_MOVIE:RATINGS_TV);
    const runtime = type==='Movie' ? Math.round(75 + rand()*75) : null; // 75-150 min
    const seasons = type==='TV Show' ? Math.max(1, Math.round(rand()*rand()*8)+1) : null;
    rows.push({
      title: genTitle(),
      type, year, genre, country, rating,
      run: type==='Movie' ? `${runtime} min` : `${seasons} season${seasons>1?'s':''}`,
      runtimeVal: runtime
    });
  }
  window.__CATALOG__ = rows;
})();
</script>
 
<script>
const catalog = window.__CATALOG__;
 
/* ---------- Chart.js global theme ---------- */
Chart.defaults.color = '#8a8a92';
Chart.defaults.font.family = "'Inter', sans-serif";
Chart.defaults.borderColor = '#2a2a2f';
const RED = '#e0202c';
const GOLD = '#d4af37';
const BLUE = '#5ba8ff';
const PALETTE = ['#e0202c','#d4af37','#5ba8ff','#8a8a92','#c9663f','#7a4fb5','#4fb587','#c94f8e','#4f8ec9','#b5904f','#9c9c46','#6a6ac9'];
 
/* ---------- helpers ---------- */
function countBy(arr, keyFn){
  const m = new Map();
  arr.forEach(d=>{ const k=keyFn(d); m.set(k,(m.get(k)||0)+1); });
  return m;
}
function topN(map, n){
  return [...map.entries()].sort((a,b)=>b[1]-a[1]).slice(0,n);
}
 
/* ---------- hero stats ---------- */
const totalTitles = catalog.length;
const totalMovies = catalog.filter(d=>d.type==='Movie').length;
const totalTV = totalTitles - totalMovies;
const countrySet = new Set(catalog.map(d=>d.country));
const genreSet = new Set(catalog.map(d=>d.genre));
 
document.getElementById('statStrip').innerHTML = `
  <div class="stat-cell"><div class="stat-num">${totalTitles}</div><div class="stat-label">Total titles</div></div>
  <div class="stat-cell"><div class="stat-num">${Math.round(totalMovies/totalTitles*100)}%</div><div class="stat-label">Are films</div></div>
  <div class="stat-cell"><div class="stat-num">${countrySet.size}</div><div class="stat-label">Countries repped</div></div>
  <div class="stat-cell"><div class="stat-num">${genreSet.size}</div><div class="stat-label">Genres tracked</div></div>
`;
 
/* ---------- marquee ---------- */
const marqueeItems = [...genreSet].concat([...genreSet]);
document.getElementById('marqueeTrack').innerHTML = marqueeItems.map(g=>`<span>${g}</span>`).join('');
 
/* ---------- growth chart (stacked movies vs tv by year) ---------- */
(function(){
  const years = [...new Set(catalog.map(d=>d.year))].sort((a,b)=>a-b);
  const movieCounts = years.map(y=>catalog.filter(d=>d.year===y&&d.type==='Movie').length);
  const tvCounts = years.map(y=>catalog.filter(d=>d.year===y&&d.type==='TV Show').length);
  new Chart(document.getElementById('growthChart'), {
    type:'bar',
    data:{
      labels:years,
      datasets:[
        {label:'Movies', data:movieCounts, backgroundColor:BLUE, stack:'s'},
        {label:'TV Shows', data:tvCounts, backgroundColor:RED, stack:'s'},
      ]
    },
    options:{
      responsive:true, maintainAspectRatio:false,
      plugins:{legend:{position:'top', labels:{boxWidth:12, font:{size:11}}}},
      scales:{
        x:{stacked:true, grid:{display:false}},
        y:{stacked:true, grid:{color:'#2a2a2f'}}
      }
    }
  });
})();
 
/* ---------- runtime histogram ---------- */
(function(){
  const runtimes = catalog.filter(d=>d.runtimeVal).map(d=>d.runtimeVal);
  const buckets = [60,80,100,120,140,160];
  const labels = ['60-80','80-100','100-120','120-140','140-160+'];
  const counts = new Array(labels.length).fill(0);
  runtimes.forEach(r=>{
    for(let i=0;i<buckets.length-1;i++){
      if(r>=buckets[i] && r<buckets[i+1]){ counts[i]++; return; }
    }
    counts[counts.length-1]++;
  });
  new Chart(document.getElementById('runtimeChart'), {
    type:'bar',
    data:{ labels, datasets:[{ label:'Films', data:counts, backgroundColor: GOLD, borderRadius:2 }]},
    options:{
      responsive:true, maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{ x:{grid:{display:false}}, y:{grid:{color:'#2a2a2f'}} }
    }
  });
})();
 
/* ---------- genre bar (horizontal) ---------- */
(function(){
  const m = countBy(catalog, d=>d.genre);
  const top = topN(m, 8);
  new Chart(document.getElementById('genreChart'), {
    type:'bar',
    data:{
      labels: top.map(t=>t[0]),
      datasets:[{ data: top.map(t=>t[1]), backgroundColor: PALETTE, borderRadius:2 }]
    },
    options:{
      indexAxis:'y',
      responsive:true, maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{ x:{grid:{color:'#2a2a2f'}}, y:{grid:{display:false}} }
    }
  });
})();
 
/* ---------- country bar ---------- */
(function(){
  const m = countBy(catalog, d=>d.country);
  const top = topN(m, 8);
  new Chart(document.getElementById('countryChart'), {
    type:'bar',
    data:{
      labels: top.map(t=>t[0]),
      datasets:[{ data: top.map(t=>t[1]), backgroundColor: RED, borderRadius:2 }]
    },
    options:{
      indexAxis:'y',
      responsive:true, maintainAspectRatio:false,
      plugins:{legend:{display:false}},
      scales:{ x:{grid:{color:'#2a2a2f'}}, y:{grid:{display:false}} }
    }
  });
})();
 
/* ---------- rating donut ---------- */
(function(){
  const m = countBy(catalog, d=>d.rating);
  const top = topN(m, 8);
  new Chart(document.getElementById('ratingChart'), {
    type:'doughnut',
    data:{
      labels: top.map(t=>t[0]),
      datasets:[{ data: top.map(t=>t[1]), backgroundColor: PALETTE, borderColor:'#141417', borderWidth:2 }]
    },
    options:{
      responsive:true, maintainAspectRatio:false,
      plugins:{ legend:{ position:'bottom', labels:{ boxWidth:10, font:{size:10} } } },
      cutout:'62%'
    }
  });
})();
 
/* ============================================================
   PROGRAMMING GUIDE (filterable table)
   ============================================================ */
const typeFilter = document.getElementById('typeFilter');
const genreFilter = document.getElementById('genreFilter');
const countryFilter = document.getElementById('countryFilter');
const ratingFilter = document.getElementById('ratingFilter');
const searchInput = document.getElementById('searchInput');
const guideBody = document.getElementById('guideBody');
const resultCount = document.getElementById('resultCount');
 
function fillSelect(sel, values){
  [...values].sort().forEach(v=>{
    const opt = document.createElement('option');
    opt.value=v; opt.textContent=v;
    sel.appendChild(opt);
  });
}
fillSelect(typeFilter, new Set(catalog.map(d=>d.type)));
fillSelect(genreFilter, genreSet);
fillSelect(countryFilter, countrySet);
fillSelect(ratingFilter, new Set(catalog.map(d=>d.rating)));
 
document.getElementById('guideCount').textContent = `${totalTitles} titles indexed`;
 
function renderTable(){
  const q = searchInput.value.trim().toLowerCase();
  const t = typeFilter.value, g = genreFilter.value, c = countryFilter.value, r = ratingFilter.value;
 
  const filtered = catalog.filter(d=>
    (!q || d.title.toLowerCase().includes(q)) &&
    (!t || d.type===t) &&
    (!g || d.genre===g) &&
    (!c || d.country===c) &&
    (!r || d.rating===r)
  ).sort((a,b)=>b.year-a.year);
 
  resultCount.textContent = `Showing ${filtered.length} of ${totalTitles} titles`;
 
  const rowsHtml = filtered.slice(0,300).map(d=>`
    <tr>
      <td class="guide-title">${d.title}</td>
      <td><span class="badge ${d.type==='Movie'?'movie':'tv'}">${d.type==='Movie'?'FILM':'SERIES'}</span></td>
      <td>${d.year}</td>
      <td>${d.country}</td>
      <td class="guide-genres">${d.genre}</td>
      <td><span class="rating-pill">${d.rating}</span></td>
      <td>${d.run}</td>
    </tr>
  `).join('');
  guideBody.innerHTML = rowsHtml || `<tr><td colspan="7" style="color:var(--muted); padding:24px;">No titles match these filters. Try widening your search.</td></tr>`;
}
 
[typeFilter, genreFilter, countryFilter, ratingFilter].forEach(el=>el.addEventListener('change', renderTable));
searchInput.addEventListener('input', renderTable);
document.getElementById('resetFilters').addEventListener('click', ()=>{
  typeFilter.value=''; genreFilter.value=''; countryFilter.value=''; ratingFilter.value=''; searchInput.value='';
  renderTable();
});
 
renderTable();
</script>
</body>
</html>
