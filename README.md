<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>

<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Segoe UI', Arial, sans-serif;
    background: #f0f4f0;
    padding: 16px;
  }

  h1 {
    text-align: center;
    color: #2e7d32;
    font-size: 20px;
    font-weight: 600;
    margin-bottom: 16px;
  }

  .acordeon {
    max-width: 600px;
    margin: auto;
  }

  /* ── Botón de día ── */
  .dia-btn {
    width: 100%;
    text-align: left;
    background: #2e7d32;
    color: white;
    padding: 14px 16px;
    margin-top: 10px;
    border-radius: 10px;
    cursor: pointer;
    font-size: 16px;
    font-weight: 600;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border: none;
    transition: background 0.2s;
  }

  .dia-btn:active { background: #1b5e20; }

  .dia-btn .chevron {
    font-size: 12px;
    opacity: 0.85;
    transition: transform 0.25s;
  }

  .dia-btn.open .chevron {
    transform: rotate(180deg);
  }

  /* ── Contenido del día ── */
  .dia-contenido {
    display: none;
    background: #ffffff;
    padding: 12px;
    border-radius: 10px;
    margin-top: 4px;
    border: 1px solid #c8e6c9;
  }

  /* ── Título de punto ── */
  .punto-titulo {
    font-size: 13px;
    font-weight: 600;
    color: #2e7d32;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    margin: 12px 0 8px;
    padding-bottom: 5px;
    border-bottom: 1px solid #e0e0e0;
  }

  .punto-titulo:first-child { margin-top: 0; }

  /* ── Grid de turnos ── */
  .turnos-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 8px;
  }

  /* ── Tarjeta de turno ── */
  .turno {
    border-radius: 8px;
    padding: 10px;
    cursor: pointer;
    border: 1px solid transparent;
    transition: opacity 0.15s, transform 0.1s;
    -webkit-tap-highlight-color: transparent;
  }

  .turno:active:not(.ocupado) { transform: scale(0.97); }

  .turno.ocupado {
    opacity: 0.7;
    cursor: not-allowed;
  }

  .turno-hora {
    font-size: 12px;
    font-weight: 600;
    color: #333;
    line-height: 1.3;
  }

  .turno-cap {
    font-size: 11px;
    color: #555;
    margin-top: 4px;
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .turno-dot {
    display: inline-block;
    width: 7px;
    height: 7px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .turno-nombres {
    font-size: 11px;
    color: #444;
    margin-top: 5px;
    line-height: 1.5;
  }

  /* Estados de color */
  .t-vacio  { background: #ffebee; border-color: #ef9a9a; }
  .t-vacio  .turno-dot { background: #e53935; }

  .t-uno    { background: #fff9c4; border-color: #f9a825; }
  .t-uno    .turno-dot { background: #f9a825; }

  .t-dos    { background: #e8f5e9; border-color: #a5d6a7; }
  .t-dos    .turno-dot { background: #43a047; }

  .t-lleno  { background: #c8e6c9; border-color: #81c784; }
  .t-lleno  .turno-dot { background: #2e7d32; }

  /* ── Leyenda ── */
  .leyenda {
    max-width: 600px;
    margin: 16px auto 0;
    display: flex;
    flex-wrap: wrap;
    gap: 10px 16px;
    font-size: 12px;
    color: #555;
    padding: 10px 12px;
    background: #fff;
    border-radius: 8px;
    border: 1px solid #ddd;
  }

  .leyenda-item {
    display: flex;
    align-items: center;
    gap: 5px;
  }

  .leyenda-dot {
    width: 9px;
    height: 9px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  /* ── Modal ── */
  .modal {
    display: none;
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.5);
    z-index: 100;
    padding: 16px;
    align-items: center;
    justify-content: center;
  }

  .modal.visible { display: flex; }

  .modal-content {
    background: white;
    padding: 24px 20px;
    width: 100%;
    max-width: 340px;
    border-radius: 14px;
  }

  .modal-content h3 {
    color: #2e7d32;
    font-size: 17px;
    margin-bottom: 6px;
  }

  .modal-content p {
    font-size: 14px;
    color: #555;
    margin-bottom: 14px;
  }

  .modal-content input[type="text"] {
    width: 100%;
    padding: 10px 12px;
    border: 1px solid #ccc;
    border-radius: 8px;
    font-size: 15px;
    outline: none;
    transition: border-color 0.2s;
  }

  .modal-content input[type="text"]:focus {
    border-color: #2e7d32;
  }

  .modal-btns {
    display: flex;
    gap: 10px;
    margin-top: 16px;
  }

  .btn-confirmar {
    flex: 1;
    padding: 10px;
    background: #2e7d32;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 15px;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.2s;
  }

  .btn-confirmar:active { background: #1b5e20; }

  .btn-cancelar {
    padding: 10px 16px;
    background: #eee;
    color: #444;
    border: none;
    border-radius: 8px;
    font-size: 15px;
    cursor: pointer;
  }

  .btn-cancelar:active { background: #ddd; }
</style>
</head>

<body>
<h1>Programación semanales de los exhibidores</h1>
    <p class="subtitulo">Selecciona el punto y el horario de tu preferencia. Escribe sólo tu nombre.</p>
<p>Por favor leer: <a href="https://wol.jw.org/es/wol/d/r4/lp-s/202015126?q=exhibidor&p=doc">Cómo predicar con los exhibidores de publicaciones</a></p>

<div id="turnos" class="acordeon"></div>

<div class="leyenda">
  <div class="leyenda-item"><div class="leyenda-dot" style="background:#e53935"></div> Disponible</div>
  <div class="leyenda-item"><div class="leyenda-dot" style="background:#f9a825"></div> 1 persona</div>
  <div class="leyenda-item"><div class="leyenda-dot" style="background:#43a047"></div> 2 personas</div>
  <div class="leyenda-item"><div class="leyenda-dot" style="background:#2e7d32"></div> Lleno</div>
</div>

<!-- Modal -->
<div id="turnoModal" class="modal">
  <div class="modal-content">
    <h3>Confirmar turno</h3>
    <p id="turnoSeleccionadoTexto"></p>
    <input type="text" id="nombreInput" placeholder="Tu nombre">
    <div class="modal-btns">
      <button class="btn-confirmar" id="confirmarBtn">Confirmar</button>
      <button class="btn-cancelar" id="cancelarBtn">Cancelar</button>
    </div>
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

const dias    = ["Lunes","Martes","Miércoles","Jueves","Viernes","Sábado"];
const puntos  = ["Tibabuyes","Afidro","Yaiti"];
const horas   = [
  "07:00 - 09:00 a.m.",
  "09:00 - 11:00 a.m.",
  "11:00 - 01:00 p.m.",
  "02:00 - 04:00 p.m.",
  "04:00 - 06:00 p.m.",
  "08:00 - 10:00 p.m."
];

const turnos = [];
dias.forEach(d => puntos.forEach(p => horas.forEach(h => turnos.push({ dia: d, punto: p, hora: h }))));

let turnoSeleccionado = null;

/* ── Acordeón ── */
function toggleDia(btn) {
  const contenido = btn.nextElementSibling;
  const estaAbierto = contenido.style.display === "block";
  document.querySelectorAll(".dia-contenido").forEach(d => d.style.display = "none");
  document.querySelectorAll(".dia-btn").forEach(b => b.classList.remove("open"));
  if (!estaAbierto) {
    contenido.style.display = "block";
    btn.classList.add("open");
  }
}

function abrirDiaActual() {
  const hoy   = new Date().getDay();
  const indice = hoy === 0 ? 5 : hoy - 1;
  const botones = document.querySelectorAll(".dia-btn");
  if (botones[indice]) {
    botones[indice].nextElementSibling.style.display = "block";
    botones[indice].classList.add("open");
  }
}

/* ── Renderizado ── */
function cargarTurnos() {
  const contenedor = document.getElementById("turnos");
  contenedor.innerHTML = "";

  const grupos = {};
  turnos.forEach((t, i) => {
    if (!grupos[t.dia])         grupos[t.dia] = {};
    if (!grupos[t.dia][t.punto]) grupos[t.dia][t.punto] = [];
    grupos[t.dia][t.punto].push({ ...t, index: i });
  });

  get(ref(db, "turnosOcupados")).then(snapshot => {
    const ocupados = snapshot.val() || {};

    Object.keys(grupos).forEach(dia => {

      /* Botón del día */
      const btn = document.createElement("button");
      btn.className = "dia-btn";
      btn.innerHTML = `${dia} <span class="chevron">▼</span>`;
      btn.onclick = () => toggleDia(btn);

      /* Contenido del día */
      const contenido = document.createElement("div");
      contenido.className = "dia-contenido";

      Object.keys(grupos[dia]).forEach(punto => {
        const titulo = document.createElement("div");
        titulo.className = "punto-titulo";
        titulo.innerText = punto;
        contenido.appendChild(titulo);

        const grid = document.createElement("div");
        grid.className = "turnos-grid";

        grupos[dia][punto].forEach(t => {
          const lista    = ocupados[t.index] || [];
          const ocupacion = lista.length;

          let clase = "t-vacio";
          if (ocupacion === 1) clase = "t-uno";
          else if (ocupacion === 2) clase = "t-dos";
          else if (ocupacion >= 3) clase = "t-lleno";

          const div = document.createElement("div");
          div.className = `turno ${clase}${ocupacion >= 3 ? " ocupado" : ""}`;

          div.innerHTML = `
            <div class="turno-hora">${t.hora}</div>
            <div class="turno-cap">
              <span class="turno-dot"></span>
              ${ocupacion}/3 ${ocupacion >= 3 ? "lleno" : "lugares"}
            </div>
            ${lista.length ? `<div class="turno-nombres">${lista.join("<br>")}</div>` : ""}
          `;

          if (ocupacion < 3) {
            div.onclick = () => abrirModal(t.index, t);
          }

          grid.appendChild(div);
        });

        contenido.appendChild(grid);
      });

      contenedor.appendChild(btn);
      contenedor.appendChild(contenido);
    });

    abrirDiaActual();
  });
}

/* ── Modal ── */
function abrirModal(index, turno) {
  turnoSeleccionado = index;
  document.getElementById("turnoSeleccionadoTexto").innerText =
    `${turno.dia} · ${turno.punto} · ${turno.hora}`;
  document.getElementById("nombreInput").value = "";
  document.getElementById("turnoModal").classList.add("visible");
  setTimeout(() => document.getElementById("nombreInput").focus(), 100);
}

function cerrarModal() {
  document.getElementById("turnoModal").classList.remove("visible");
}

document.getElementById("confirmarBtn").onclick = () => {
  const nombre = document.getElementById("nombreInput").value.trim();
  if (!nombre) return alert("Escribe tu nombre");

  const refTurno = ref(db, `turnosOcupados/${turnoSeleccionado}`);
  get(refTurno).then(snap => {
    let lista = snap.val() || [];
    if (!Array.isArray(lista)) lista = [lista];
    if (lista.length >= 3) {
      alert("Este turno ya está lleno");
      cerrarModal();
      return;
    }
    lista.push(nombre);
    set(refTurno, lista).then(() => cerrarModal());
  });
};

document.getElementById("cancelarBtn").onclick = cerrarModal;

/* Cerrar modal al tocar el fondo */
document.getElementById("turnoModal").onclick = (e) => {
  if (e.target === document.getElementById("turnoModal")) cerrarModal();
};

/* Escucha cambios en tiempo real */
onValue(ref(db, "turnosOcupados"), cargarTurnos);
</script>

</body>
</html>
