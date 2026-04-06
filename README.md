<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>

<style>
body {
  font-family: Arial, sans-serif;
  background: #f4f6f8;
  padding: 15px;
  text-align: center;
}

.acordeon {
  max-width: 1000px;
  margin: auto;
}

.dia-btn {
  width: 100%;
  text-align: left;
  background: #2e7d32;
  color: white;
  padding: 12px;
  margin-top: 12px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 18px;
}

.dia-contenido {
  display: none;
  background: #ffffff;
  padding: 10px;
  border-radius: 8px;
  margin-bottom: 10px;
}

h3 {
  text-align:left;
  color:#2e7d32;
}

.turno {
  margin: 8px;
  padding: 10px;
  width: 180px;
  border-radius: 10px;
  border: 2px solid #ddd;
  cursor: pointer;
  font-weight: bold;
  display:inline-block;
}

.turno.ocupado {
  background:#c8e6c9;
  cursor:not-allowed;
}

.nombres {
  font-size:12px;
  margin-top:6px;
  text-align:left;
  font-weight:normal;
}

.modal {
  display:none;
  position:fixed;
  inset:0;
  background:rgba(0,0,0,0.5);
}

.modal-content {
  background:white;
  margin:15% auto;
  padding:20px;
  width:320px;
  border-radius:12px;
}

button {
  padding:8px 14px;
  border:none;
  border-radius:6px;
  cursor:pointer;
}

#confirmarBtn {background:#2e7d32;color:white;}
#cancelarBtn {background:#ccc;}
</style>
</head>

<body>
<div id="turnos" class="acordeon"></div>

<div id="turnoModal" class="modal">
  <div class="modal-content">
    <h3>Confirmar turno</h3>
    <p id="turnoSeleccionadoTexto"></p>
    <input type="text" id="nombreInput" placeholder="Nombre">
    <br><br>
    <button id="confirmarBtn">Confirmar</button>
    <button id="cancelarBtn">Cancelar</button>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
import { getDatabase, ref, get, set, onValue } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js";

const firebaseConfig = {
  apiKey: "AIzaSyCPDLePibPdmmQdDxQJmPWHc-mhEylVgG4",
  authDomain: "turnos-semanales.firebaseapp.com",
  databaseURL: "https://turnos-semanales-default-rtdb.firebaseio.com",
  projectId: "turnos-semanales",
  storageBucket: "turnos-semanales.firebasestorage.app",
  messagingSenderId: "1097727159040",
  appId: "1:1097727159040:web:c2ba55472ea3563e65e1e5"
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

const dias=["Lunes","Martes","Miércoles","Jueves","Viernes","Sábado"];
const puntos=["Tibabuyes","Afidro","Yaiti"];

const horas=[
"07:00 - 09:00 a.m.",
"09:00 - 11:00 a.m.",
"11:00 - 01:00 p.m.",
"01:00 - 03:00 p.m.",
"03:00 - 05:00 p.m.",
"05:00 - 07:00 p.m."
];

const turnos=[];
dias.forEach(d=>puntos.forEach(p=>horas.forEach(h=>turnos.push({dia:d,punto:p,hora:h}))));

let turnoSeleccionado=null;

function toggleDia(el){
document.querySelectorAll(".dia-contenido").forEach(d=>d.style.display="none");
el.nextElementSibling.style.display="block";
}

function abrirDiaActual(){
const hoy = new Date().getDay(); 
const indice = hoy === 0 ? 5 : hoy-1; // domingo abre sábado
const botones = document.querySelectorAll(".dia-btn");
if(botones[indice]){
botones[indice].nextElementSibling.style.display="block";
}
}

function cargarTurnos(){

const contenedor=document.getElementById("turnos");
contenedor.innerHTML="";

const grupos={};
turnos.forEach((t,i)=>{
if(!grupos[t.dia])grupos[t.dia]={};
if(!grupos[t.dia][t.punto])grupos[t.dia][t.punto]=[];
grupos[t.dia][t.punto].push({...t,index:i});
});

get(ref(db,"turnosOcupados")).then(snapshot=>{

const ocupados=snapshot.val()||{};

Object.keys(grupos).forEach(dia=>{

const btn=document.createElement("div");
btn.className="dia-btn";
btn.innerText=dia;
btn.onclick=()=>toggleDia(btn);

const contenido=document.createElement("div");
contenido.className="dia-contenido";

Object.keys(grupos[dia]).forEach(punto=>{

const titulo=document.createElement("h3");
titulo.innerText=punto;
contenido.appendChild(titulo);

grupos[dia][punto].forEach(t=>{

const div=document.createElement("div");
div.className="turno";

const lista=ocupados[t.index]||[];
const ocupacion=lista.length;

div.innerHTML=`
${t.hora} (${ocupacion}/3)
<div class="nombres">${lista.join("<br>")}</div>
`;

if(ocupacion===3){
div.style.background="#c8e6c9";
div.classList.add("ocupado");
}else if(ocupacion===2){
div.style.background="#c8e6c9";
div.onclick=()=>abrirModal(t.index,t);
}else if(ocupacion===1){
div.style.background="#fff9c4";
div.onclick=()=>abrirModal(t.index,t);
}else{
div.style.background="#ffcdd2";
div.onclick=()=>abrirModal(t.index,t);
}

contenido.appendChild(div);

});

});

contenedor.appendChild(btn);
contenedor.appendChild(contenido);

});

abrirDiaActual();

});

}

function abrirModal(index,turno){
turnoSeleccionado=index;
document.getElementById("turnoSeleccionadoTexto").innerText=
`${turno.dia} - ${turno.punto} - ${turno.hora}`;
document.getElementById("nombreInput").value="";
document.getElementById("turnoModal").style.display="block";
}

function cerrarModal(){
document.getElementById("turnoModal").style.display="none";
}

document.getElementById("confirmarBtn").onclick=()=>{
const nombre=document.getElementById("nombreInput").value.trim();
if(!nombre)return alert("Escribe el nombre");

const refTurno=ref(db,`turnosOcupados/${turnoSeleccionado}`);

get(refTurno).then(snap=>{
let lista=snap.val()||[];
if(!Array.isArray(lista))lista=[lista];
if(lista.length>=3){
alert("Turno lleno");
cerrarModal();
return;
}
lista.push(nombre);
set(refTurno,lista).then(()=>cerrarModal());
});
};

document.getElementById("cancelarBtn").onclick=cerrarModal;

onValue(ref(db,"turnosOcupados"),cargarTurnos);
</script>

</body>
</html>
