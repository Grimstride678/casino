<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>BARO Casino Style Gaming</title>
  <style>
    body {
      background: radial-gradient(circle, #2c2c2c, #000);
      color: white;
      font-family: 'Segoe UI', sans-serif;
      text-align: center;
      padding: 2rem;
    }
    h1 {
      font-size: 3rem;
      color: #f00;
    }
    .game-container {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 1.5rem;
      margin-top: 2rem;
    }
    .game {
      background-color: #111;
      border: 2px solid #f00;
      border-radius: 12px;
      padding: 1rem;
      transition: 0.3s;
    }
    .game:hover {
      transform: scale(1.05);
      cursor: pointer;
    }
    button {
      padding: 0.5rem 1rem;
      margin-top: 1rem;
      background-color: #f00;
      color: white;
      border: none;
      border-radius: 6px;
    }
    input {
      padding: 0.3rem;
      border-radius: 5px;
      border: none;
    }
    #result, #coins-display {
      margin-top: 1rem;
      font-weight: bold;
    }
    .auth {
      margin-bottom: 2rem;
    }
  </style>
</head>
<body>
  <h1>🎰 BARO კაზინო თამაშები 🎲</h1>

  <!-- რეგისტრაცია / შესვლა -->
  <div class="auth">
    <input type="text" id="username" placeholder="მომხმარებლის სახელი" />
    <button onclick="register()">რეგისტრაცია</button>
    <button onclick="login()">შესვლა</button>
    <div id="auth-status"></div>
  </div>

  <!-- მონეტების ჩვენება -->
  <div id="coins-display">💰 მონეტები: <span id="coins">0</span></div>

  <div class="game-container">
    <!-- 1. რულეტკა -->
    <div class="game">
      <h2>რულეტკა</h2>
      <button onclick="tryPlay(playRoulette)">დაატრიალე</button>
      <div id="roulette-result"></div>
    </div>

    <!-- 2. გამოიცანი ციფრი -->
    <div class="game">
      <h2>გამოიცანი ციფრი</h2>
      <input type="number" id="guess" placeholder="1-10" />
      <button onclick="tryPlay(guessNumber)">გამოიცანი</button>
      <div id="guess-result"></div>
    </div>

    <!-- 3. იღბლიანი ბორბალი -->
    <div class="game">
      <h2>იღბლიანი ბორბალი</h2>
      <button onclick="tryPlay(spinWheel)">დაატრიალე ბორბალი</button>
      <div id="wheel-result"></div>
    </div>

    <!-- 4. სლოტ მანქანა -->
    <div class="game">
      <h2>სლოტი</h2>
      <button onclick="tryPlay(playSlot)">სლოტი</button>
      <div id="slot-result"></div>
    </div>

    <!-- 5. კამათელი (Dice Game) -->
    <div class="game">
      <h2>კამათელი</h2>
      <button onclick="tryPlay(rollDice)">გადააგდე</button>
      <div id="dice-result"></div>
    </div>
  </div>

  <script>
    let users = {};
    let currentUser = null;

    function register() {
      const username = document.getElementById("username").value.trim();
      if (username === "") {
        document.getElementById("auth-status").innerText = "შეიყვანე სახელი";
        return;
      }
      if (!users[username]) {
        users[username] = { coins: 100 };
        document.getElementById("auth-status").innerText = `რეგისტრაცია წარმატებულია: ${username}`;
      } else {
        document.getElementById("auth-status").innerText = "მომხმარებელი უკვე არსებობს";
      }
    }

    function login() {
      const username = document.getElementById("username").value.trim();
      if (users[username]) {
        currentUser = username;
        updateCoins();
        document.getElementById("auth-status").innerText = `შესვლა წარმატებულია: ${username}`;
      } else {
        document.getElementById("auth-status").innerText = "მომხმარებელი არ არსებობს";
      }
    }

    function updateCoins() {
      if (currentUser) {
        document.getElementById("coins").innerText = users[currentUser].coins;
      }
    }

    function rewardCoins(amount) {
      if (currentUser) {
        users[currentUser].coins += amount;
        updateCoins();
      }
    }

    // ამ ფუნქციით ხდება მოთამაშის ქულების შემოწმება და ჩამოკლება 20 ქულის.
    function tryPlay(gameFunction) {
      if (!currentUser) {
        alert("გთხოვთ, შედით სისტემაში");
        return;
      }
      if (users[currentUser].coins < 20) {
        alert("თქვენ არ გაქვთ საკმარისი მონეტა თამაშის დასაწყებად (20 მონეტა საჭიროა)");
        return;
      }
      users[currentUser].coins -= 20;
      updateCoins();
      gameFunction();
    }

    function playRoulette() {
      const number = Math.floor(Math.random() * 37);
      document.getElementById("roulette-result").innerText = `🎯 ვარდნა: ${number}`;
      if (number === 7) rewardCoins(20);
    }

    function guessNumber() {
      const userGuess = parseInt(document.getElementById("guess").value);
      if (isNaN(userGuess) || userGuess < 1 || userGuess > 10) {
        document.getElementById("guess-result").innerText = "❗ შეიყვანე რიცხვი 1-დან 10-მდე";
        return;
      }
      const correct = Math.floor(Math.random() * 10) + 1;
      const result = (userGuess === correct) ? "🎉 სწორია! +10 მონეტა" : `❌ არასწორია. სწორი იყო ${correct}`;
      document.getElementById("guess-result").innerText = result;
      if (userGuess === correct) rewardCoins(10);
    }

    function spinWheel() {
      const outcomes = ["2x", "3x", "4x", "1x", "0x"];
      const multipliers = { "0x": 0, "1x": 1, "2x": 2, "3x": 3, "4x": 4 };
      const random = outcomes[Math.floor(Math.random() * outcomes.length)];
      document.getElementById("wheel-result").innerText = `🍀 შედეგი: ${random}`;
      rewardCoins(5 * multipliers[random]);
    }

    function playSlot() {
      const symbols = ["🍒", "🍋", "🔔", "💎", "7️⃣"];
      const slot1 = symbols[Math.floor(Math.random() * symbols.length)];
      const slot2 = symbols[Math.floor(Math.random() * symbols.length)];
      const slot3 = symbols[Math.floor(Math.random() * symbols.length)];
      const result = (slot1 === slot2 && slot2 === slot3) ? "🏆 ჯეკპოტი! +50 მონეტა" : "🙂 სცადე ისევ.";
      document.getElementById("slot-result").innerText = `${slot1} ${slot2} ${slot3} → ${result}`;
      if (slot1 === slot2 && slot2 === slot3) rewardCoins(50);
    }

    function rollDice() {
      const die1 = Math.floor(Math.random() * 6) + 1;
      const die2 = Math.floor(Math.random() * 6) + 1;
      document.getElementById("dice-result").innerText = `🎲 ${die1} + ${die2} = ${die1 + die2}`;
      if (die1 + die2 === 12) rewardCoins(25);
    }
  </script>
</body>
</html>
