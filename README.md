<!DOCTYPE html>
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

    h1, h2, p {
      text-align: center;
    }

    ul {
      list-style: none;
      padding: 0;
      max-width: 600px;
      margin: auto;
    }

    li {
      background: white;
      margin-bottom: 15px;
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

    /* Cuando está elegido */
    .bloqueado input,
    .bloqueado button {
      display: none;
    }
  </style>
</head>

<body>

  <h1>Proyecto Matrimonio: fase de financiamiento 💸❤️</h1>

  <p>
    Elige un regalo y confirma tu elección.  
    <br>Los regalos ya elegidos no estarán disponibles.
  </p>

  <ul>

    <li id="vasos">
      <strong>Juego de vasos</strong><br>
      <span id="vasos-nombre"></span><br>
      <input type="text" id="vasos-input" placeholder="Tu nombre"><br>
      <button onclick="elegirRegalo('vasos')">Elegir</button>
    </li>

    <li id="licuadora">
      <strong>Licuadora</strong><br>
      <span id="licuadora-nombre"></span><br>
      <input type="text" id="licuadora-input" placeholder="Tu nombre"><br>
      <button onclick="elegirRegalo('licuadora')">Elegir</button>
    </li>

    <li id="sartenes">
      <strong>Juego de sartenes</strong><br>
      <span id="sartenes-nombre"></span><br>
      <input type="text" id="sartenes-input" placeholder="Tu nombre"><br>
      <button onclick="elegirRegalo('sartenes')">Elegir</button>
    </li>

  </ul>

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

    // Elegir regalo
    window.elegirRegalo = function(id) {
      const input = document.getElementById(id + "-input");
      const nombre = input.value.trim();

      if (nombre === "") {
        alert("Por favor escribe tu nombre");
        return;
      }
