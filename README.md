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
      display: none; /* Timer ausblenden */
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
        <button class="speed-button active" data-speed="slow">🐢 langsam</button>
        <button class="speed-button" data-speed="medium">🐇 mittel</button>
        <button class="speed-button" data-speed="fast">⚡ schnell</button>
      </div>
    </div>

    <div style="display: flex; gap: 0.5rem; justify-content: center; flex-wrap: wrap; margin-bottom: 0.5rem;">
      <button id="startButton" class="primary">⏱️ Start</button>
    </div>

    <div class="timer-display" id="timerDisplay"></div>

    <div class="words" id="wordsContainer">
      <span class="hint">Drücke „Start“, um mit der Blitzleseübung zu beginnen.</span>
    </div>
  </div>

  <script>
    /* =======================
       DEINE WORTLISTEN
    ======================= */
    const UEBUNGEN = {
      stufe1: [
        "bin", "zuletzt", "wieder", "wenig", "warum", "sehr", "voll", "wann",
        "kommen", "essen", "schwimmen", "die Schuhe", "das Geld", "laufen",
        "die Nacht", "die Eltern", "die Erde", "müssen", "schneiden", "ins",
        "man", "nicht", "die Säfte", "der Bäcker", "hoffen"
      ],

      stufe2: [
        "bin", "zuletzt", "wieder", "wenig", "warum", "sehr", "voll", "wann",
        "kommen", "essen", "schwimmen", "die Schuhe", "das Geld", "laufen",
        "die Nacht", "die Eltern", "die Erde", "müssen", "schneiden", "ins",
        "man", "nicht", "die Säfte", "der Bäcker", "hoffen", "deinem", "welchen",
        "der Abend", "die Ente", "das Telefon", "die Tande", "der Quatsch",
        "über", "so", "um", "lernen", "der Partner"
      ],

      stufe3: [
        "bin", "zuletzt", "wieder", "wenig", "warum", "sehr", "voll", "wann",
        "kommen", "essen", "schwimmen", "die Schuhe", "das Geld", "laufen",
        "die Nacht", "die Eltern", "die Erde", "müssen", "schneiden", "ins",
        "man", "nicht", "die Säfte", "der Bäcker", "hoffen", "deinem", "welchen",
        "der Abend", "die Ente", "das Telefon", "die Tande", "der Quatsch",
        "über", "so", "um", "lernen", "der Partner", "die Stunde", "der Löwe",
        "die Blüte", "das Gemüse", "legen", "oder", "schon", "das Mädchen",
        "die Sonne"
      ]
    };

    /* OPTIONAL: Eigene IDs für Kinder */
    const ID_AUF_STUFE = {};

    /* =======================
       TEMPO
    ======================= */
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

      if (id && ID_AUF_STUFE[id]) key = ID_AUF_STUFE[id];
      if (!UEBUNGEN[key]) key = "stufe1";

      return {
        keyAnzeigen: id || key,
        keyIntern: key,
        wörter: UEBUNGEN[key]
      };
    }

    const { keyAnzeigen, keyIntern, wörter } = bestimmeListe();
    document.getElementById("levelLabel").textContent = keyAnzeigen + " (" + keyIntern + ")";

    let wordTimer = null;
    let currentIndex = 0;

    const wordsContainer = document.getElementById("wordsContainer");
    const startButton = document.getElementById("startButton");

    function renderSingleWord(word) {
      wordsContainer.innerHTML = "";
      const span = document.createElement("span");
      span.className = "word";
      span.textContent = word;
      wordsContainer.appendChild(span);
    }

    function stopTimers() {
      if (wordTimer) clearInterval(wordTimer);
      wordTimer = null;
      startButton.disabled = false;
    }

    function startExercise() {
      stopTimers();
      currentIndex = 0;

      renderSingleWord(wörter[currentIndex]);
      startButton.disabled = true;

      const interval = SPEED_INTERVALS[currentSpeed];
      wordTimer = setInterval(() => {
        currentIndex = (currentIndex + 1) % wörter.length;
        renderSingleWord(wörter[currentIndex]);
      }, interval);
    }

    startButton.addEventListener("click", startExercise);

    /* Tempo-Buttons */
    const speedButtons = document.querySelectorAll(".speed-button");
    speedButtons.forEach((btn) => {
      btn.addEventListener("click", () => {
        speedButtons.forEach((b) => b.classList.remove("active"));
        btn.classList.add("active");
        currentSpeed = btn.dataset.speed;

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
