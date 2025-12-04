<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <title>Blitzlesen</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    * {
      box-sizing: border-box;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
    }

    body {
      margin: 0;
      padding: 0;
      background: #f4f4f5;
      display: flex;
      justify-content: center;
      align-items: flex-start;
      min-height: 100vh;
    }

    .app {
      background: #ffffff;
      margin: 1.5rem;
      padding: 1.5rem;
      border-radius: 1rem;
      max-width: 700px;
      width: 100%;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.07);
    }

    h1 {
      margin-top: 0;
      font-size: 1.8rem;
      text-align: center;
    }

    .meta {
      display: flex;
      flex-wrap: wrap;
      gap: 0.75rem;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 1rem;
      font-size: 0.95rem;
    }

    .badge {
      display: inline-flex;
      align-items: center;
      padding: 0.25rem 0.6rem;
      border-radius: 999px;
      background: #eef2ff;
      font-size: 0.85rem;
    }

    .badge span {
      font-weight: 600;
      margin-left: 0.25rem;
    }

    button {
      border: none;
      border-radius: 999px;
      padding: 0.5rem 1.1rem;
      font-size: 0.95rem;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 0.35rem;
      transition: transform 0.05s ease, box-shadow 0.1s ease, background 0.15s ease;
    }

    button.primary {
      background: #4f46e5;
      color: #ffffff;
      box-shadow: 0 4px 12px rgba(79, 70, 229, 0.35);
    }

    button.primary:disabled {
      background: #a5b4fc;
      cursor: default;
      box-shadow: none;
      transform: none;
    }

    .timer-display {
      font-size: 1.4rem;
      font-weight: 600;
      text-align: center;
      margin-top: 0.25rem;
    }

    .words {
      margin-top: 1.5rem;
      padding: 1.5rem;
      border-radius: 0.75rem;
      background: #f9fafb;
      border: 1px dashed #e5e7eb;
      min-height: 120px;
      display: flex;
      flex-wrap: wrap;
      gap: 0.7rem;
      justify-content: center;
      align-items: center;
      text-align: center;
    }

    .word {
      padding: 0.25rem 0.5rem;
      border-radius: 0.5rem;
      font-size: 2rem;
      font-weight: 600;
    }

    .speed-controls {
      display: flex;
      align-items: center;
      gap: 0.4rem;
      flex-wrap: wrap;
      font-size: 0.9rem;
    }

    .speed-button {
      background: #e4e4e7;
      padding: 0.35rem 0.7rem;
      border-radius: 999px;
      font-size: 0.95rem;
    }

    .speed-button.active {
      background: #22c55e;
      color: #ffffff;
      box-shadow: 0 3px 8px rgba(34, 197, 94, 0.45);
    }

    .speed-icon {
      font-size: 1.1rem;
    }

    @media (max-width: 600px) {
      .word {
        font-size: 1.6rem;
      }
    }
  </style>
</head>
<body>
  <div class="app">
    <h1>Blitzlesen</h1>

    <div class="meta">
      <div class="badge">
        Stufe / ID: <span id="levelLabel">–</span>
      </div>

      <div class="speed-controls">
        <span>Tempo:</span>
        <button class="speed-button active" data-speed="slow">
          <span class="speed-icon">🐢</span> langsam
        </button>
        <button class="speed-button" data-speed="medium">
          <span class="speed-icon">🐇</span> mittel
        </button>
        <button class="speed-button" data-speed="fast">
          <span class="speed-icon">⚡</span> schnell
        </button>
      </div>
    </div>

    <div style="display: flex; gap: 0.5rem; justify-content: center; flex-wrap: wrap; margin-bottom: 0.5rem;">
      <button id="startButton" class="primary">⏱️ Start</button>
    </div>

    <div class="timer-display" id="timerDisplay">Bereit</div>

    <div class="words" id="wordsContainer">
      <span class="hint">Drücke „Start“, um mit der Blitzleseübung zu beginnen.</span>
    </div>

    <div class="footer">
      Blitzlesen – einfache Übungswebseite für die Grundschule
    </div>
  </div>

  <script>
    // ============================
    //   DEINE WORTLISTEN
    // ============================
    const UEBUNGEN = {
      stufe1: [
        "dumm",
        "schwimmen",
        "schnell",
        "vergessen",
        "Zimmer",
        "zusammen",
        "Boden",
        "Dings",
        "Seite",
        "Stein",
        "Tasche",
        "Wagen",
        "Zeitung",
        "Woche",
        "hoffen",
        "essen"
      ],

      stufe2: [
        "dumm",
        "schwimmen",
        "Nummer",
        "Kuss",
        "wissen",
        "schnell",
        "vergessen",
        "Zimmer",
        "zusammen",
        "Boden",
        "Dings",
        "Seite",
        "Stein",
        "Tasche",
        "Wagen",
        "Zeitung",
        "Woche",
        "hoffen",
        "essen",
        "schenken",
        "Woche",
        "Gesicht"
      ],

      stufe3: [
        "dumm",
        "schwimmen",
        "Nummer",
        "Kuss",
        "wissen",
        "schnell",
        "vergessen",
        "Zimmer",
        "zusammen",
        "Boden",
        "Dings",
        "Seite",
        "Stein",
        "Tasche",
        "Wagen",
        "Zeitung",
        "Woche",
        "hoffen",
        "essen",
        "schenken",
        "Woche",
        "Gesicht",
        "beginnen",
        "Nase",
        "Hals",
        "helfen",
        "heute",
        "gehören"
      ]
    };

    // Optional: IDs → Stufen
    const ID_AUF_STUFE = {};

    // ============================
    //   TEMPO-EINSTELLUNGEN
    // ============================
    const SPEED_INTERVALS = {
      slow: 2000,   // 🐢
      medium: 1000, // 🐇
      fast: 500     // ⚡
    };

    let currentSpeed = "slow";

    function getQueryParam(name) {
      const params = new URLSearchParams(window.location.search);
      return params.get(name);
    }

    function bestimmeListe() {
      const id = getQueryParam("id");
      let key = id || "stufe1";

      if (id && ID_AUF_STUFE[id]) {
        key = ID_AUF_STUFE[id];
      }

      let liste = UEBUNGEN[key];
      if (!liste) {
        key = "stufe1";
        liste = UEBUNGEN[key];
      }

      return { keyAnzeigen: id || key, keyIntern: key, wörter: liste };
    }

    function renderSingleWord(word) {
      const container = document.getElementById("wordsContainer");
      container.innerHTML = "";

      const span = document.createElement("span");
      span.className = "word";
      span.textContent = word;
      container.appendChild(span);
    }

    const { keyAnzeigen, keyIntern, wörter } = bestimmeListe();
    document.getElementById("levelLabel").textContent =
      keyAnzeigen + " (" + keyIntern + ")";

    let wordTimer = null;
    let secondTimer = null;
    let currentIndex = 0;
    let remainingSeconds = 30;

    const timerDisplay = document.getElementById("timerDisplay");
    const startButton = document.getElementById("startButton");

    function stopTimers() {
      if (wordTimer) clearInterval(wordTimer);
      if (secondTimer) clearInterval(secondTimer);
      wordTimer = null;
      secondTimer = null;
      startButton.disabled = false;
    }

    function startExercise() {
      if (!wörter || wörter.length === 0) return;

      stopTimers();
      currentIndex = 0;
      remainingSeconds = 30;

      renderSingleWord(wörter[currentIndex]);
      timerDisplay.textContent = remainingSeconds + " Sekunden";
      startButton.disabled = true;

      // Countdown (Gesamtdauer)
      secondTimer = setInterval(() => {
        remainingSeconds--;
        if (remainingSeconds <= 0) {
          stopTimers();
          timerDisplay.textContent = "Fertig!";
        } else {
          timerDisplay.textContent = remainingSeconds + " Sekunden";
        }
      }, 1000);

      // Wortwechsel nach Tempo
      const interval = SPEED_INTERVALS[currentSpeed];
      wordTimer = setInterval(() => {
        currentIndex = (currentIndex + 1) % wörter.length;
        renderSingleWord(wörter[currentIndex]);
      }, interval);
    }

    startButton.addEventListener("click", startExercise);

    // Tempo-Buttons aktivieren
    const speedButtons = document.querySelectorAll(".speed-button");
    speedButtons.forEach((btn) => {
      btn.addEventListener("click", () => {
        speedButtons.forEach((b) => b.classList.remove("active"));
        btn.classList.add("active");

        currentSpeed = btn.getAttribute("data-speed");

        // Wenn Übung läuft → Tempo sofort anpassen
        if (wordTimer) {
          clearInterval(wordTimer);
          const interval = SPEED_INTERVALS[currentSpeed];
          wordTimer = setInterval(() => {
            currentIndex = (currentIndex + 1) % wörter.length;
            renderSingleWord(wörter[currentIndex]);
          }, interval);
        }
      });
    });
  </script>
</body>
</html>
