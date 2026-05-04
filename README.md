<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1" />
<title>DDA Pro LC V21</title>
<style>
  :root{
    --bg:#07111f;
    --card:#111b2f;
    --card2:#17233d;
    --txt:#edf4ff;
    --mut:#9eb2d2;
    --acc:#2dd4bf;
    --acc2:#60a5fa;
    --ok:#4ade80;
    --bad:#fb7185;
    --warn:#facc15;
  }

  body{
    background:var(--bg);
    color:var(--txt);
    font-family:sans-serif;
    padding:20px;
    margin:0;
  }

  h2,h3,h4{
    margin-top:0;
  }

  .card{
    background:var(--card);
    padding:20px;
    border-radius:20px;
    margin-bottom:20px;
    box-shadow:0 0 20px rgba(45,212,191,.12);
  }

  .hidden{
    display:none !important;
  }

  .grid{
    display:grid;
    grid-template-columns:1fr 1fr;
    gap:10px;
  }

  button{
    width:100%;
    padding:15px;
    margin:5px 0;
    border-radius:15px;
    border:none;
    font-weight:bold;
    background:linear-gradient(135deg,var(--acc),var(--acc2));
    color:#06101d;
    box-shadow:0 0 12px rgba(45,212,191,.35);
  }

  button:active{
    transform:scale(.98);
  }

  input,
  select{
    width:100%;
    box-sizing:border-box;
    padding:14px;
    margin:8px 0 14px;
    border-radius:14px;
    border:2px solid var(--acc);
    background:var(--card2);
    color:var(--txt);
    font-size:16px;
    outline:none;
  }

  input:focus,
  select:focus{
    border-color:var(--acc2);
    box-shadow:0 0 12px rgba(96,165,250,.4);
  }

  label{
    display:block;
    color:var(--acc);
    font-weight:bold;
    margin-top:10px;
  }

  .swap-tab{
    margin-top:18px;
    padding:16px;
    background:var(--card2);
    border-radius:18px;
    border:1px solid var(--acc);
    box-shadow:0 0 14px rgba(45,212,191,.35);
  }

  .swap-result{
    margin-top:18px;
    padding:16px;
    border-radius:16px;
    font-weight:bold;
    line-height:1.5;
  }

  .swap-ok{
    background:rgba(74,222,128,.18);
    border:2px solid var(--ok);
    color:var(--ok);
  }

  .swap-warning{
    background:rgba(250,204,21,.18);
    border:2px solid var(--warn);
    color:var(--warn);
  }

  .swap-bad{
    background:rgba(251,113,133,.18);
    border:2px solid var(--bad);
    color:var(--bad);
  }

  .suggestion-table{
    width:100%;
    border-collapse:collapse;
    margin-top:15px;
    overflow:hidden;
    border-radius:14px;
  }

  .suggestion-table th,
  .suggestion-table td{
    padding:12px;
    border-bottom:1px solid rgba(255,255,255,.12);
    text-align:left;
    font-size:14px;
  }

  .suggestion-table th{
    color:var(--acc);
    background:rgba(45,212,191,.12);
  }

  .history-item{
    margin-top:10px;
    padding:12px;
    border-radius:14px;
    background:rgba(96,165,250,.12);
    border:1px solid var(--acc2);
    line-height:1.45;
  }

  .muted{
    color:var(--mut);
  }

  .score-high{
    color:var(--ok);
    font-weight:bold;
  }

  .score-mid{
    color:var(--acc2);
    font-weight:bold;
  }

  .score-low{
    color:var(--bad);
    font-weight:bold;
  }

  @media(max-width:640px){
    body{
      padding:12px;
    }

    .grid{
      grid-template-columns:1fr;
    }

    .suggestion-table th,
    .suggestion-table td{
      padding:9px;
      font-size:12px;
    }
  }
</style>
</head>
<body>

<section id="loginCard" class="card">
  <h2>Connexion</h2>
  <input id="login" placeholder="ID">
  <input id="pass" type="password" placeholder="Pass">
  <button onclick="connect()">Connexion</button>
</section>

<section id="appCard" class="card hidden">
  <h2>Profil et Options</h2>
  <p class="muted">Bienvenue dans DDA Pro LC V21.</p>
</section>

<section id="appCard2" class="card hidden">
  <h2>Rotations</h2>

  <div class="grid">
    <button onclick="showTab('mesRotations')">Mes rotations</button>
    <button onclick="showTab('suggestionsAuto')">Suggestions auto</button>
    <button onclick="showTab('moteurSwap')">Moteur de Swap</button>
    <button onclick="showTab('historique')">Historique</button>
  </div>

  <div id="mesRotations" class="swap-tab">
    <h3>Mes rotations</h3>
    <p class="muted">Zone des rotations actuelles.</p>
  </div>

  <div id="suggestionsAuto" class="swap-tab hidden" style="display:none;">
    <h3>Suggestions auto</h3>
    <p class="muted">Le score de prédiction est calculé avec ton historique enregistré.</p>

    <table class="suggestion-table">
      <thead>
        <tr>
          <th>Destination</th>
          <th>Demandes</th>
          <th>Obtenues</th>
          <th>Score</th>
        </tr>
      </thead>
      <tbody id="suggestionsTableBody">
        <tr>
          <td colspan="4">Aucune donnée historique pour le moment.</td>
        </tr>
      </tbody>
    </table>
  </div>

  <div id="moteurSwap" class="swap-tab hidden" style="display:none;">
    <h3>Moteur de Swap</h3>

    <label for="rotationActuelle">Rotation actuelle à échanger</label>
    <input id="rotationActuelle" placeholder="Ex : JFK 4J 320€">

    <label for="rotationRecherchee">Rotation recherchée / Destination souhaitée</label>
    <input id="rotationRecherchee" placeholder="Ex : NRT 5J 410€">

    <button onclick="calcSwap()">Calculer le swap</button>

    <div id="swapResult" class="swap-result hidden" style="display:none;"></div>
  </div>

  <div id="historique" class="swap-tab hidden" style="display:none;">
    <h3>Historique</h3>

    <label for="historiqueRotation">Rotation demandée</label>
    <input id="historiqueRotation" placeholder="Ex : JFK 4J 320€">

    <label for="historiqueResultat">Résultat</label>
    <select id="historiqueResultat">
      <option value="obtenue">Obtenue</option>
      <option value="non_obtenue">Non obtenue</option>
    </select>

    <button onclick="saveHistorique()">Enregistrer résultat</button>

    <div id="historiqueMessage" class="swap-result hidden" style="display:none;"></div>

    <h4>Résultats enregistrés</h4>
    <div id="historiqueListe"></div>
  </div>
</section>

<section id="appCard3" class="card hidden">
  <h2>Zone V21</h2>
  <p class="muted">Section disponible pour les futures fonctions.</p>
</section>

<section id="appCard4" class="card hidden">
  <h2>Options avancées</h2>
  <p class="muted">Section disponible pour les futures fonctions.</p>
</section>

<section id="appCard5" class="card hidden">
  <h2>Statistiques</h2>
  <p class="muted">Section disponible pour les futures fonctions.</p>
</section>

<section id="appCard6" class="card hidden">
  <h2>Export</h2>
  <p class="muted">Section disponible pour les futures fonctions.</p>
</section>

<script>
function connect(){
  const login = document.getElementById('login').value;
  const pass = document.getElementById('pass').value;

  if(login && pass){
    document.getElementById('loginCard').classList.add('hidden');
    document.getElementById('loginCard').style.display = 'none';

    ['appCard','appCard2','appCard3','appCard4','appCard5','appCard6'].forEach(id => {
      const section = document.getElementById(id);
      if(section){
        section.classList.remove('hidden');
        section.style.display = 'block';
      }
    });

    showTab('mesRotations');
    analyseHistorique();
    afficherHistorique();
  }
}

function showTab(tabId){
  ['mesRotations','suggestionsAuto','moteurSwap','historique'].forEach(id => {
    const tab = document.getElementById(id);
    if(tab){
      tab.classList.add('hidden');
      tab.style.display = 'none';
    }
  });

  const selectedTab = document.getElementById(tabId);
  if(selectedTab){
    selectedTab.classList.remove('hidden');
    selectedTab.style.display = 'block';
  }

  if(tabId === 'suggestionsAuto'){
    analyseHistorique();
  }

  if(tabId === 'historique'){
    afficherHistorique();
  }
}

function calcSwap(){
  const actuelle = document.getElementById('rotationActuelle').value.trim();
  const recherchee = document.getElementById('rotationRecherchee').value.trim();
  const result = document.getElementById('swapResult');

  result.className = 'swap-result';
  result.classList.remove('hidden');
  result.style.display = 'block';

  if(!actuelle || !recherchee){
    result.classList.add('swap-bad');
    result.innerHTML = 'Merci de remplir les deux champs avant de lancer le calcul.';
    return;
  }

  const dataActuelle = analyseRotation(actuelle);
  const dataRecherchee = analyseRotation(recherchee);

  if(!dataActuelle.destination || !dataRecherchee.destination){
    result.classList.add('swap-bad');
    result.innerHTML = 'Destination non reconnue. Utilise un format simple comme : JFK 4J 320€.';
    return;
  }

  const differenceJours = Math.abs(dataActuelle.jours - dataRecherchee.jours);
  const differenceIndemnites = dataRecherchee.indemnites - dataActuelle.indemnites;

  let avis = '';

  if(differenceJours <= 1){
    result.classList.add('swap-ok');
    avis = 'Swap techniquement envisageable.';
  }else if(differenceJours <= 2){
    result.classList.add('swap-warning');
    avis = 'Swap possible, mais à vérifier manuellement.';
  }else{
    result.classList.add('swap-bad');
    avis = 'Swap techniquement peu favorable.';
  }

  let rentabilite = '';

  if(differenceIndemnites > 0){
    rentabilite = '+' + differenceIndemnites + '€ estimés';
  }else if(differenceIndemnites < 0){
    rentabilite = differenceIndemnites + '€ estimés';
  }else{
    rentabilite = 'Rentabilité équivalente';
  }

  result.innerHTML = `
    <div>${avis}</div>
    <div>Rotation actuelle : ${dataActuelle.destination} / ${dataActuelle.jours} jour(s) / ${dataActuelle.indemnites}€</div>
    <div>Rotation recherchée : ${dataRecherchee.destination} / ${dataRecherchee.jours} jour(s) / ${dataRecherchee.indemnites}€</div>
    <div>Rentabilité estimée : ${rentabilite}</div>
  `;
}

function analyseRotation(texte){
  const t = texte.toUpperCase();

  const destination = t.match(/\b[A-Z]{3}\b/);
  const jours = t.match(/(\d+)\s*(J|JOUR|JOURS)/);
  const indemnites = t.match(/(\d+)\s*€/);

  return {
    destination: destination ? destination[0] : '',
    jours: jours ? parseInt(jours[1]) : 0,
    indemnites: indemnites ? parseInt(indemnites[1]) : 0
  };
}

function saveHistorique(){
  const rotation = document.getElementById('historiqueRotation').value.trim();
  const resultat = document.getElementById('historiqueResultat').value;
  const message = document.getElementById('historiqueMessage');

  message.className = 'swap-result';
  message.classList.remove('hidden');
  message.style.display = 'block';

  if(!rotation){
    message.classList.add('swap-bad');
    message.innerHTML = 'Merci d’indiquer une rotation demandée.';
    return;
  }

  const data = analyseRotation(rotation);

  if(!data.destination){
    message.classList.add('swap-bad');
    message.innerHTML = 'Destination non reconnue. Utilise un format comme : JFK 4J 320€.';
    return;
  }

  const historique = getHistorique();

  historique.push({
    rotation: rotation,
    destination: data.destination,
    resultat: resultat,
    date: new Date().toLocaleDateString('fr-FR')
  });

  try{
    localStorage.setItem('ddaProHistorique', JSON.stringify(historique));
  }catch(error){
    message.classList.add('swap-bad');
    message.innerHTML = 'Impossible d’enregistrer dans localStorage sur cet appareil.';
    return;
  }

  message.classList.add('swap-ok');
  message.innerHTML = 'Résultat enregistré avec succès. Les données seront conservées après rafraîchissement.';

  document.getElementById('historiqueRotation').value = '';

  afficherHistorique();
  analyseHistorique();
}

function getHistorique(){
  try{
    const data = localStorage.getItem('ddaProHistorique');

    if(!data){
      return [];
    }

    return JSON.parse(data);
  }catch(error){
    return [];
  }
}

function afficherHistorique(){
  const liste = document.getElementById('historiqueListe');
  const historique = getHistorique();

  if(!liste){
    return;
  }

  if(historique.length === 0){
    liste.innerHTML = '<p class="muted">Aucun résultat enregistré pour le moment.</p>';
    return;
  }

  liste.innerHTML = historique.slice().reverse().map(item => {
    const resultatTexte = item.resultat === 'obtenue' ? 'Obtenue' : 'Non obtenue';

    return `
      <div class="history-item">
        <div><strong>${item.destination}</strong> - ${item.rotation}</div>
        <div>Résultat : ${resultatTexte}</div>
        <div>Date : ${item.date}</div>
      </div>
    `;
  }).join('');
}

function analyseHistorique(){
  const historique = getHistorique();
  const tbody = document.getElementById('suggestionsTableBody');

  if(!tbody){
    return;
  }

  if(historique.length === 0){
    tbody.innerHTML = `
      <tr>
        <td colspan="4">Aucune donnée historique pour le moment.</td>
      </tr>
    `;
    return;
  }

  const stats = {};

  historique.forEach(item => {
    if(!stats[item.destination]){
      stats[item.destination] = {
        demandes: 0,
        obtenues: 0
      };
    }

    stats[item.destination].demandes++;

    if(item.resultat === 'obtenue'){
      stats[item.destination].obtenues++;
    }
  });

  tbody.innerHTML = Object.keys(stats).sort().map(destination => {
    const demandes = stats[destination].demandes;
    const obtenues = stats[destination].obtenues;
    const score = Math.round((obtenues / demandes) * 100);

    let scoreClass = 'score-low';

    if(score >= 70){
      scoreClass = 'score-high';
    }else if(score >= 40){
      scoreClass = 'score-mid';
    }

    return `
      <tr>
        <td>${destination}</td>
        <td>${demandes}</td>
        <td>${obtenues}</td>
        <td class="${scoreClass}">${score}%</td>
      </tr>
    `;
  }).join('');
}

document.addEventListener('DOMContentLoaded', function(){
  analyseHistorique();
  afficherHistorique();
});
</script>
</body>
</html>
-
