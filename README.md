# gestionnaire-de-circonscription-
Logiciel de gestion d'une circonscription scolaire 

<html>
<head>
<meta charset="UTF-8">
<title>Plateforme Nationale de Gestion</title>

<style>
body{
font-family:Arial;
margin:0;
background:#eef2f7;
}

header{
background:#1e3799;
color:white;
padding:15px;
text-align:center;
}

.container{
padding:20px;
}

.card{
background:white;
padding:20px;
margin:15px 0;
border-radius:10px;
box-shadow:0 4px 10px rgba(0,0,0,0.1);
}

input, select{
padding:8px;
margin:5px;
width:90%;
}

button{
padding:8px 15px;
margin:5px;
background:#1e3799;
color:white;
border:none;
border-radius:5px;
cursor:pointer;
}

img{
width:120px;
height:120px;
border-radius:50%;
object-fit:cover;
}

.hidden{
display:none;
}

table{
width:100%;
border-collapse:collapse;
}

th, td{
border:1px solid #ccc;
padding:8px;
text-align:center;
}
</style>
</head>

<body>

<header>
<h2>Gestion Nationale de Circonscription</h2>
</header>

<div class="container">

<div id="loginCard" class="card">
<h3>Connexion</h3>

<select id="role">
<option value="concepteur">Concepteur</option>
<option value="inspecteur">Inspecteur</option>
<option value="directeur">Directeur</option>
<option value="maitre">Maître</option>
</select>

<input type="password" id="password" placeholder="Mot de passe standard">
<button onclick="login()">Se connecter</button>
</div>

<div id="dashboard" class="hidden">

<div class="card">
<h3>Profil Utilisateur</h3>
<img id="photoPreview" src="https://via.placeholder.com/120">
<input type="file" accept="image/*" onchange="loadPhoto(event)">
<input type="text" id="nom" placeholder="Nom complet">
<input type="text" id="fonction" placeholder="Fonction">
<input type="text" id="structure" placeholder="École / Circonscription">
<button onclick="saveProfile()">Enregistrer Profil</button>
<button onclick="logout()">Déconnexion</button>
</div>

<div id="gestionEleves" class="card hidden">
<h3>Gestion des Élèves</h3>
<input type="text" id="eleveNom" placeholder="Nom élève">
<input type="text" id="eleveClasse" placeholder="Classe">
<button onclick="ajouterEleve()">Ajouter</button>

<table>
<thead>
<tr>
<th>Nom</th>
<th>Classe</th>
</tr>
</thead>
<tbody id="listeEleves"></tbody>
</table>
</div>

<div id="gestionMaitres" class="card hidden">
<h3>Gestion des Maîtres</h3>
<input type="text" id="maitreNom" placeholder="Nom maître">
<button onclick="ajouterMaitre()">Ajouter</button>

<table>
<thead>
<tr>
<th>Nom</th>
</tr>
</thead>
<tbody id="listeMaitres"></tbody>
</table>
</div>

<div class="card">
<button onclick="genererPDF()">Générer Rapport PDF</button>
</div>

</div>
</div>

<script>

const motDePasseStandard = "1234";

function login(){
let role = document.getElementById("role").value;
let pass = document.getElementById("password").value;

if(pass === motDePasseStandard){
localStorage.setItem("role", role);
document.getElementById("loginCard").classList.add("hidden");
document.getElementById("dashboard").classList.remove("hidden");
chargerInterface(role);
chargerDonnees();
}else{
alert("Mot de passe incorrect");
}
}

function chargerInterface(role){
if(role === "maitre"){
document.getElementById("gestionEleves").classList.remove("hidden");
}

if(role === "directeur"){
document.getElementById("gestionEleves").classList.remove("hidden");
document.getElementById("gestionMaitres").classList.remove("hidden");
}

if(role === "inspecteur" || role === "concepteur"){
document.getElementById("gestionEleves").classList.remove("hidden");
document.getElementById("gestionMaitres").classList.remove("hidden");
}
}

function logout(){
localStorage.clear();
location.reload();
}

function saveProfile(){
let profile = {
nom: nom.value,
fonction: fonction.value,
structure: structure.value,
photo: photoPreview.src
};
localStorage.setItem("profile", JSON.stringify(profile));
alert("Profil enregistré");
}

function loadPhoto(event){
let reader = new FileReader();
reader.onload = function(){
photoPreview.src = reader.result;
}
reader.readAsDataURL(event.target.files[0]);
}

function ajouterEleve(){
let eleves = JSON.parse(localStorage.getItem("eleves")) || [];
eleves.push({nom: eleveNom.value, classe: eleveClasse.value});
localStorage.setItem("eleves", JSON.stringify(eleves));
afficherEleves();
}

function afficherEleves(){
let eleves = JSON.parse(localStorage.getItem("eleves")) || [];
listeEleves.innerHTML="";
eleves.forEach(e=>{
listeEleves.innerHTML += `<tr><td>${e.nom}</td><td>${e.classe}</td></tr>`;
});
}

function ajouterMaitre(){
let maitres = JSON.parse(localStorage.getItem("maitres")) || [];
maitres.push({nom: maitreNom.value});
localStorage.setItem("maitres", JSON.stringify(maitres));
afficherMaitres();
}

function afficherMaitres(){
let maitres = JSON.parse(localStorage.getItem("maitres")) || [];
listeMaitres.innerHTML="";
maitres.forEach(m=>{
listeMaitres.innerHTML += `<tr><td>${m.nom}</td></tr>`;
});
}

function chargerDonnees(){
let profile = JSON.parse(localStorage.getItem("profile"));
if(profile){
nom.value = profile.nom;
fonction.value = profile.fonction;
structure.value = profile.structure;
photoPreview.src = profile.photo;
}
afficherEleves();
afficherMaitres();
}

function genererPDF(){
window.print();
}

</script>

</body>
</html>
