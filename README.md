<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>Juego de Niveles - Espacio</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      margin-top: 40px;
    }
    #game, #shop, #final {
      display: none;
    }
    .counter {
      font-size: 48px;
      margin: 20px;
    }
    .timer {
      font-size: 24px;
    }
    #shop-button {
      position: fixed;
      top: 20px;
      right: 20px;
      font-size: 28px;
      background: none;
      border: none;
      cursor: pointer;
    }
    input {
      font-size: 18px;
      margin: 5px;
      padding: 6px;
    }
    button {
      font-size: 18px;
      padding: 8px 16px;
      margin-top: 10px;
      cursor: pointer;
    }
  </style>
  <!-- Aquí el script de anuncios Adsense, pon tu ID de cliente -->
  <script async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js?client=TU-ID-ADSENSE" crossorigin="anonymous"></script>
</head>
<body>

<!-- LOGIN -->
<div id="login">
  <h2>Iniciar sesión</h2>
  <input type="text" id="username" placeholder="Usuario" /><br />
  <input type="password" id="password" placeholder="Contraseña" /><br />
  <button onclick="login()">Entrar</button>
  <p id="login-error" style="color: red;"></p>
</div>

<!-- JUEGO -->
<div id="game">
  <h2>Nivel <span id="level">1</span></h2>
  <h3>Meta: <span id="goal">200</span> puntos</h3>
  <p>Presiona ESPACIO para sumar puntos (1 por vez)</p>
  <div class="counter" id="counter">0</div>
  <div class="timer" id="timer">Tiempo: 0.00s</div>
  <div id="result"></div>
  <p>Monedas: <span id="coins">0</span></p>
  <button onclick="openShop()" id="shop-button" title="Abrir tienda">🛒</button>
</div>

<!-- TIENDA -->
<div id="shop">
  <h2>Tienda de mejoras</h2>
  <p>Monedas disponibles: <span id="shop-coins">0</span></p>
  <button onclick="buyUpgrade(2, 20)">+2 por 20 monedas</button><br />
  <button onclick="buyUpgrade(5, 50)">+5 por 50 monedas</button><br />
  <button onclick="buyUpgrade(10, 70)">+10 por 70 monedas</button><br />
  <button onclick="earnByAd()">Ver anuncio → +10 monedas</button><br /><br />
  <p id="shop-message"></p>
  <button onclick="closeShop()">Volver al juego</button>
</div>

<!-- FINAL -->
<div id="final">
  <h1>🎉 ¡Felicidades! Terminaste los 10 niveles 🎉</h1>
  <h2>Ganaste $1000 pesos 💵</h2>
  <p>El premio será entregado por el creador del juego</p>
</div>

<!-- ANUNCIO ADS -->
<div id="ads" style="margin-top: 30px;">
  <ins
    class="adsbygoogle"
    style="display:block"
    data-ad-client="TU-ID-ADSENSE"
    data-ad-slot="1234567890"
    data-ad-format="auto"
    data-full-width-responsive="true"
  ></ins>
  <script>
    (adsbygoogle = window.adsbygoogle || []).push({});
  </script>
</div>

<script>
  // Variables del juego
  let level = 1;
  const maxLevel = 10;
  let goal = 200;
  let count = 0;
  let coins = 0;
  let increment = 1;
  let startTime = null;
  let finished = false;
  let spacePressed = false;

  function login() {
    const user = document.getElementById("username").value;
    const pass = document.getElementById("password").value;
    if (user === "jorge" && pass === "jorge") {
      document.getElementById("login").style.display = "none";
      document.getElementById("game").style.display = "block";
      updateUI();
    } else {
      document.getElementById("login-error").textContent =
        "Usuario o contraseña incorrectos.";
    }
  }

  function updateUI() {
    document.getElementById("counter").textContent = count;
    document.getElementById("coins").textContent = coins;
    document.getElementById("level").textContent = level;
    document.getElementById("goal").textContent = goal;
    document.getElementById("shop-coins").textContent = coins;
  }

  // Detecta cada presión única de espacio
  document.addEventListener("keydown", function (event) {
    if (document.getElementById("game").style.display !== "block") return;

    if (event.code === "Space" && !finished) {
      event.preventDefault();

      if (spacePressed) return; // evita suma repetida al mantener tecla
      spacePressed = true;

      if (count === 0) startTime = performance.now();

      count += increment;
      if (count >= goal) {
        finished = true;
        let totalTime = ((performance.now() - startTime) / 1000).toFixed(2);
        document.getElementById("result").innerHTML = `<p>¡Nivel completado!</p><p>Tiempo: ${totalTime}s</p>`;
        coins += 20;
        updateUI();

        setTimeout(() => {
          if (level < maxLevel) {
            level++;
            goal += 50;
            count = 0;
            increment = 1;
            finished = false;
            startTime = null;
            document.getElementById("result").textContent = "";
            updateUI();
          } else {
            document.getElementById("game").style.display = "none";
            document.getElementById("final").style.display = "block";
          }
        }, 2000);
      }
      updateUI();

      if (startTime && !finished) {
        let elapsed = ((performance.now() - startTime) / 1000).toFixed(2);
        document.getElementById("timer").textContent = `Tiempo: ${elapsed}s`;
      }
    }

    // Código secreto para obtener muchas monedas (solo tú)
    if (event.shiftKey && event.key.toLowerCase() === "m") {
      coins += 100;
      updateUI();
      document.getElementById("shop-message").textContent = "¡Has ganado 100 monedas secretas!";
      return;
    }
  });

  // Resetea al soltar la barra espaciadora para poder contar la próxima
  document.addEventListener("keyup", function (event) {
    if (event.code === "Space") {
      spacePressed = false;
    }
  });

  // Abrir y cerrar tienda
  function openShop() {
    document.getElementById("game").style.display = "none";
    document.getElementById("shop").style.display = "block";
    document.getElementById("shop-message").textContent = "";
    updateUI();
  }

  function closeShop() {
    document.getElementById("shop").style.display = "none";
    document.getElementById("game").style.display = "block";
    updateUI();
  }

  // Comprar mejoras
  function buyUpgrade(value, cost) {
    if (coins >= cost) {
      coins -= cost;
      increment += value;
      document.getElementById("shop-message").textContent = `¡Mejora comprada! +${value} puntos por espacio`;
      updateUI();
    } else {
      document.getElementById("shop-message").textContent = "No tienes suficientes monedas.";
    }
  }

  // Ganar monedas viendo anuncios (simulado)
  function earnByAd() {
    // Aquí deberías integrar anuncios reales si quieres monetizar
    coins += 10;
    document.getElementById("shop-message").textContent = "Ganaste 10 monedas por ver un anuncio.";
    updateUI();
  }
</script>

</body>
</html>
