<!DOCTYPE html>
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

h2{
  margin-top:40px;
}

h3 {
  width: 100%;
  text-align: left;
  max-width: 1000px;
  margin: 20px auto 10px;
  color: #2e7d32;
}

#turnos {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  max-width: 1000px;
  margin: auto;
}

.turno {
  margin: 8px;
  padding: 12px;
  width: 180px;
  border-radius: 10px;
  background: #ffffff;
  border: 2px solid #ddd;
  cursor: pointer;
  transition: all 0.25s ease;
  font-weight: bold;
}

.turno.ocupado {
  background: #c8e6c9;
  border-color: #2e7d32;
  cursor: not-allowed;
}

.modal {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.5);
}

.modal-content {
  background: white;
  margin: 15% auto;
  padding: 20px;
  width: 320px;
  border-radius: 12px;
}

.modal-content input {
  width: 100%;
  padding: 8px;
  margin: 10px 0;
}

button {
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

#confirmarBtn {
  background: #2e7d32;
  color: white;
}

#cancelarBtn {
  background: #ccc;
}
</style>
</head>

<body>

<h1>Inscripción de Turnos</h1>

<div id="turnos"></div>

<div id="turnoModal" class="modal">
  <div class="modal-content">
    <h3>Confirmar turno</h3>
    <p id="turnoSeleccionadoTexto"></p>
    <input type="text" id="nombreInput" placeholder="Nombre">
    <button id="confirmarBtn">Confirmar</button>
    <button id="cancelarBtn">Cancelar</button>
  </div>
</div>

<script type="module">
import { initializeApp } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js";
import { getDatabase, ref, get, set, onValue } from "https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js";

const firebaseConfig = {
  apiKey: "TU API KEY",
  authDomain: "TU AUTH",
  databaseURL: "TU DB",
  projectId: "TU ID",
  storageBucket: "",
  messagingSenderId: "",
  appId: ""
};

const app = initializeApp(firebaseConfig);
const db = getDatabase(app);

const dias = ["Lunes","Martes","Miércoles","Jueves","Viernes","Sábado"];
const puntos = ["Tibabuyes","Afidro","Yaiti"];

const horas = [
"07:00 - 09:00 a.m.",
"09:00 - 11:00 a.m.",
"11:00 - 01:00 p.m.",
"01:00 - 03:00 p.m.",
"03:00 - 05:00 p.m.",
"05:00 - 07:00 p.m."
];

const turnos = [];

dias.forEach(dia=>{
  puntos.forEach(punto=>{
    horas.forEach(hora=>{
      turnos.push({dia,punto,hora});
    });
  });
});

let turnoSeleccionado = null;

function cargarTurnos(){

const contenedor = document.getElementById("turnos");
contenedor.innerHTML="";

const grupos={};

turnos.forEach((t,i)=>{
  if(!grupos[t.dia]) grupos[t.dia]={};
  if(!grupos[t.dia][t.punto]) grupos[t.dia][t.punto]=[];
  grupos[t.dia][t.punto].push({...t,index:i});
});

get(ref(db,"turnosOcupados")).then(snapshot=>{

const ocupados = snapshot.val() || {};

Object.keys(grupos).forEach(dia=>{

const tituloDia=document.createElement("h2");
tituloDia.innerText=dia;
contenedor.appendChild(tituloDia);

Object.keys(grupos[dia]).forEach(punto=>{

const titulo=document.createElement("h3");
titulo.innerText=punto;
contenedor.appendChild(titulo);

grupos[dia][punto].forEach(t=>{

const div=document.createElement("div");
div.className="turno";

const ocupacion = ocupados[t.index] ? ocupados[t.index].length : 0;

div.innerText = t.hora + " ("+ocupacion+"/3)";

if(ocupacion===3){
div.style.background="#c8e6c9";
div.classList.add("ocupado");
}

else if(ocupacion===2){
div.style.background="#c8e6c9";
div.onclick=()=>abrirModal(t.index,t);
}

else if(ocupacion===1){
div.style.background="#fff9c4";
div.onclick=()=>abrirModal(t.index,t);
}

else{
div.style.background="#ffcdd2";
div.onclick=()=>abrirModal(t.index,t);
}

contenedor.appendChild(div);

});

});

});

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
if(!nombre) return alert("Escribe el nombre");

const refTurno=ref(db,`turnosOcupados/${turnoSeleccionado}`);

get(refTurno).then(snap=>{

let lista=snap.val() || [];

if(!Array.isArray(lista)) lista=[lista];

if(lista.length>=3){
alert("Turno lleno");
cerrarModal();
return;
}

lista.push(nombre);

set(refTurno,lista).then(()=>{
cerrarModal();
});

});

};

document.getElementById("cancelarBtn").onclick=cerrarModal;

onValue(ref(db,"turnosOcupados"),cargarTurnos);

</script>

</body>
</html>
