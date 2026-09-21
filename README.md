<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Vox-GPT 8.0.0 — GEE VOX TECH</title>
  <meta name="theme-color" content="#00f0ff">

  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@600;800&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">

  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    :root {
      --bg: #05050a;
      --panel: #0d0d17;
      --panel2: #11111f;
      --border: #24243d;
      --cyan: #00f0ff;
      --magenta: #ff00e5;
      --text: #eeeeff;
      --dim: #8585a8;
    }

    body {
      min-height: 100vh;
      background:
        radial-gradient(circle at 15% 20%, #00f0ff12, transparent 35%),
        radial-gradient(circle at 85% 80%, #ff00e512, transparent 35%),
        var(--bg);
      color: var(--text);
      font-family: Inter, Arial, sans-serif;
    }

    header {
      padding: 22px;
      text-align: center;
      border-bottom: 1px solid var(--border);
      background: #080811cc;
      backdrop-filter: blur(10px);
    }

    .logo {
      font-family: Orbitron, sans-serif;
      font-size: 30px;
      font-weight: 800;
      color: var(--cyan);
      text-shadow: 0 0 15px var(--cyan);
    }

    .logo span {
      color: var(--magenta);
      text-shadow: 0 0 15px var(--magenta);
    }

    .version {
      color: var(--dim);
      margin-top: 7px;
      font-size: 13px;
    }

    .container {
      width: 94%;
      max-width: 1100px;
      margin: 25px auto;
    }

    /* MODULES */

    .modules {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(135px, 1fr));
      gap: 12px;
      margin-bottom: 24px;
    }

    .module {
      padding: 16px 10px;
      border: 1px solid #00f0ff33;
      border-radius: 13px;
      background: var(--panel);
      text-align: center;
      cursor: pointer;
      transition: .25s;
    }

    .module:hover {
      border-color: var(--cyan);
      box-shadow: 0 0 18px #00f0ff33;
      transform: translateY(-3px);
    }

    .module span {
      display: block;
      font-size: 25px;
      margin-bottom: 8px;
    }

    .module small {
      color: #ddd;
    }

    /* CHAT */

    .chat {
      background: var(--panel);
      border: 1px solid #00f0ff33;
      border-radius: 16px;
      overflow: hidden;
      box-shadow: 0 0 30px #0008;
    }

    .chat-title {
      padding: 17px;
      border-bottom: 1px solid var(--border);
      color: var(--cyan);
      font-family: Orbitron, sans-serif;
      font-weight: bold;
    }

    .messages {
      height: 430px;
      overflow-y: auto;
      padding: 18px;
    }

    .message {
      margin-bottom: 15px;
      padding: 13px 16px;
      border-radius: 13px;
      max-width: 88%;
      line-height: 1.6;
      white-space: pre-wrap;
      word-wrap: break-word;
    }

    .user {
      margin-left: auto;
      background: linear-gradient(135deg, #ff00e522, #8a2be222);
      border: 1px solid #ff00e555;
    }

    .ai {
      background: #151522;
      border: 1px solid #00f0ff33;
    }

    .loading {
      color: var(--cyan);
    }

    /* INPUT */

    .input-area {
      display: flex;
      gap: 10px;
      padding: 15px;
      border-top: 1px solid var(--border);
    }

    textarea {
      flex: 1;
      resize: none;
      min-height: 55px;
      max-height: 150px;
      padding: 15px;
      background: #05050a;
      color: white;
      border: 1px solid #33334d;
      border-radius: 11px;
      outline: none;
      font-family: inherit;
      font-size: 15px;
    }

    textarea:focus {
      border-color: var(--cyan);
      box-shadow: 0 0 12px #00f0ff22;
    }

    button {
      border: none;
      border-radius: 10px;
      padding: 0 22px;
      background: linear-gradient(135deg, var(--cyan), var(--magenta));
      color: #000;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      box-shadow: 0 0 18px #00f0ff66;
    }

    button:disabled {
      opacity: .5;
      cursor: not-allowed;
    }

    .status {
      text-align: center;
      margin-top: 18px;
      color: var(--dim);
      font-size: 13px;
    }

    .online {
      color: #00ff75;
    }

    footer {
      text-align: center;
      padding: 30px;
      color: #555;
      font-size: 13px;
    }

    @media(max-width:700px) {
      .logo {
        font-size: 25px;
      }

      .messages {
        height: 400px;
      }

      .input-area {
        flex-direction: column;
      }

      button {
        min-height: 48px;
      }
    }
  </style>
</head>

<body>

<header>
  <div class="logo">⚡ VOX<span>-GPT</span></div>
  <div class="version">
    GEE VOX TECH • Version 8.0.0
  </div>
</header>

<div class="container">

  <div class="modules">

    <div class="module" onclick="moduleClick('Intelligence')">
      <span>🤖</span>
      <small>Intelligence</small>
    </div>

    <div class="module" onclick="moduleClick('Recherche')">
      <span>🔎</span>
      <small>Recherche</small>
    </div>

    <div class="module" onclick="moduleClick('Programmation')">
      <span>💻</span>
      <small>Programmation</small>
    </div>

    <div class="module" onclick="moduleClick('Documents')">
      <span>📄</span>
      <small>Documents</small>
    </div>

    <div class="module" onclick="moduleClick('Vision')">
      <span>👁️</span>
      <small>Vision</small>
    </div>

    <div class="module" onclick="moduleClick('Création')">
      <span>🎨</span>
      <small>Création</small>
    </div>

    <div class="module" onclick="moduleClick('Voix et Audio')">
      <span>🎙️</span>
      <small>Voix / Audio</small>
    </div>

    <div class="module" onclick="moduleClick('Données')">
      <span>📊</span>
      <small>Données</small>
    </div>

    <div class="module" onclick="moduleClick('Agents')">
      <span>⚙️</span>
      <small>Agents</small>
    </div>

    <div class="module" onclick="moduleClick('API')">
      <span>🔌</span>
      <small>API</small>
    </div>

    <div class="module" onclick="moduleClick('Sécurité')">
      <span>🛡️</span>
      <small>Sécurité</small>
    </div>

    <div class="module" onclick="moduleClick('Mémoire')">
      <span>🧠</span>
      <small>Mémoire</small>
    </div>

    <div class="module" onclick="moduleClick('Applications')">
      <span>📱</span>
      <small>Applications</small>
    </div>

    <div class="module" onclick="moduleClick('Architecture')">
      <span>🏗️</span>
      <small>Architecture</small>
    </div>

  </div>

  <div class="chat">

    <div class="chat-title">
      💬 Discussion avec Vox-GPT
    </div>

    <div id="messages" class="messages">

      <div class="message ai">
        👋 Bonjour !

        Je suis <b>Vox-GPT 8.0.0</b>,
        l'assistant IA de GEE VOX TECH.

        Comment puis-je t'aider ?
      </div>

    </div>

    <div class="input-area">

      <textarea
        id="message"
        placeholder="Écris ton message à Vox-GPT..."
        onkeydown="handleKey(event)"
      ></textarea>

      <button id="sendButton" onclick="sendMessage()">
        Envoyer
      </button>

    </div>

  </div>

  <div id="status" class="status">
    Vérification de Vox-GPT...
  </div>

</div>

<footer>
  © 2026 GEE VOX TECH — VOX-GPT 8.0.0
</footer>

<script>

const messages = document.getElementById("messages");
const input = document.getElementById("message");
const sendButton = document.getElementById("sendButton");
const status = document.getElementById("status");

/* Ajouter un message */

function addMessage(text, type) {

  const div = document.createElement("div");

  div.className = "message " + type;

  div.textContent = text;

  messages.appendChild(div);

  messages.scrollTop = messages.scrollHeight;

  return div;
}

/* Envoyer à Netlify */

async function sendMessage() {

  const text = input.value.trim();

  if (!text) return;

  addMessage(text, "user");

  input.value = "";

  sendButton.disabled = true;

  const loading = addMessage(
    "⚡ Vox-GPT réfléchit...",
    "ai loading"
  );

  try {

    const response = await fetch(
      "/.netlify/functions/chat",
      {
        method: "POST",

        headers: {
          "Content-Type": "application/json"
        },

        body: JSON.stringify({
          message: text
        })
      }
    );

    const data = await response.json();

    loading.remove();

    if (!response.ok || data.error) {

      addMessage(
        "❌ " + (data.error || "Erreur du serveur."),
        "ai"
      );

      return;
    }

    addMessage(
      data.text || "Aucune réponse reçue.",
      "ai"
    );

  } catch (error) {

    loading.remove();

    addMessage(
      "❌ Impossible de contacter Vox-GPT.",
      "ai"
    );

    console.error(error);

  } finally {

    sendButton.disabled = false;

    input.focus();
  }
}

/* Entrée clavier */

function handleKey(event) {

  if (
    event.key === "Enter" &&
    !event.shiftKey
  ) {

    event.preventDefault();

    sendMessage();
  }
}

/* Modules */

function moduleClick(name) {

  input.value =
    "Active le module " +
    name +
    " et explique-moi ce que tu peux faire avec.";

  input.focus();
}

/* Vérification */

async function checkServer() {

  try {

    const response = await fetch(
      "/.netlify/functions/chat",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          message: "Réponds simplement : ONLINE"
        })
      }
    );

    if (response.ok) {

      status.innerHTML =
        "● <span class='online'>Vox-GPT est en ligne</span>";

    } else {

      status.textContent =
        "● Fonction IA non configurée";

    }

  } catch {

    status.textContent =
      "● Vox-GPT n'est pas disponible";

  }
}

checkServer();

</script>

</body>
</html>