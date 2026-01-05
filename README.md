<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Proyecto Matrimonio: fase de financiamiento</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f4f4f4;
      padding: 20px;
    }

    h1, p {
      text-align: center;
    }

    ul {
      list-style: none;
      padding: 0;
      max-width: 700px;
      margin: auto;
    }

    li {
      background: white;
      margin-bottom: 12px;
      padding: 15px;
      border-radius: 8px;
    }

    .elegido {
      color: green;
      font-weight: bold;
    }

    input {
      padding: 6px;
      margin-top: 5px;
      width: 65%;
    }

    button {
      padding: 6px 10px;
      margin-top: 5px;
      cursor: pointer;
    }

    .bloqueado input,
    .bloqueado button {
      display: none;
    }
  </style>
</head>

<body>

  <h1>Proyecto Matrimonio: fase de financiamiento 💸❤️</h1>

  <p>Elige un regalo Para Santiago y Kathleen. Los ya seleccionados no estarán disponibles.</p>

  <ul id="listaRegalos"></ul>

  <!-- 🔥 FIREBASE -->
  <script type="module">
    import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
    import { getDatabase, ref, set, onValue }
      from "https://www.gstatic.com/firebasejs/10.7.1/firebase-database.js";

    const firebaseConfig = {
      apiKey: "AIzaSyBBJwB7goplMb2WJpBvJU5rsvoueD84glg",
      authDomain: "despedida-ba71a.firebaseapp.com",
      databaseURL: "https://despedida-ba71a-default-rtdb.firebaseio.com",
      projectId: "despedida-ba71a",
      storageBucket: "despedida-ba71a.firebasestorage.app",
      messagingSenderId: "30414791076",
      appId: "1:30414791076:web:08d0d7494477ca5f912131"
    };

    const app = initializeApp(firebaseConfig);
    const db = getDatabase(app);

    // 📝 LISTA DE REGALOS
    const regalos = [
      "Juego de ollas y limpiones",
      "Refractarias",
      "Cuchillos",
      "Cucharones",
      "Vasos",
      "Pocillos",
      "Exprimidor de limón",
      "2 mugs",
      "Pela papas",
      "Frascos de especias",
      "Rallador",
      "Delantal de cocina",
      "Porta vasos",
      "Individuales de mesa",
      "Escoba, trapero y recogedor",
      "Azucarero y salero",
      "Bandeja",
      "Ensaladera",
      "Porta retratos",
      "Juego de copas",
      "Caneca de basura",
      "Organizadores (plástico o tela)",
      "Almohadas",
      "Toallas para cuerpo",
      "Jabonera",
      "Olleta para chocolate",
      "Molinillo",
      "Caldero para arroz",
      "Juego de porta alimentos",
      "Juego de sartenes",
      "Coladores",
      "Cesta de ropa",
      "Juego de cubiertos",
      "Frascos para arroz, café y granos",
      "Jarra para jugo",
      "Tabla para picar",
      "Set de utensilios de cocina",
      "Tupper herméticos",
      "Papelera para baño",
      "Ganchos para clóset",
      "Abrebotellas y sacacorchos"
    ];

    const lista = document.getElementById("listaRegalos");

    // Crear HTML dinámicamente
    regalos.forEach((nombre, index) => {
      const id = "regalo" + index;

      const li = document.createElement("li");
      li.id = id;

      li.innerHTML = `
        <strong>${nombre}</strong><br>
        <span id="${id}-estado"></span><br>
        <input type="text" id="${id}-input" placeholder="Tu nombre"><br>
        <button onclick="elegirRegalo('${id}')">Elegir</button>
      `;

      lista.appendChild(li);
    });

    // Elegir regalo
    window.elegirRegalo = function(id) {
      const input = document.getElementById(id + "-input");
      const nombre = input.value.trim();

      if (!nombre) {
        alert("Por favor escribe tu nombre");
        return;
      }

      set(ref(db, "regalos/" + id), { nombre });
    };

    // Escuchar Firebase
    onValue(ref(db, "regalos"), (snapshot) => {
      const data = snapshot.val() || {};

      document.querySelectorAll("li").forEach(li => {
        li.classList.remove("bloqueado");
        const span = li.querySelector("span");
        if (span) span.innerText = "";
      });

      for (let id in data) {
        const li = document.getElementById(id);
        const span = document.getElementById(id + "-estado");

        if (li && span) {
          span.innerText = "Regalo elegido ✅";
          span.className = "elegido";
          li.classList.add("bloqueado");
        }
      }
    });
  </script>

</body>
</html>
