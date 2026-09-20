# vox-gpt
Vox-GPT 8.0.0 — assistant IA de GEE VOX TECH
import express from "express";
import cors from "cors";
import dotenv from "dotenv";
import OpenAI from "openai";
import path from "path";
import { fileURLToPath } from "url";

dotenv.config();

const app = express();
const PORT = process.env.PORT || 3000;

const __filename = fileURLToPath(import.meta.url);
const __dirname = path.dirname(__filename);

if (!process.env.OPENAI_API_KEY) {
  console.warn("⚠️ OPENAI_API_KEY n'est pas configurée.");
}

const client = new OpenAI({
  apiKey: process.env.OPENAI_API_KEY
});

app.use(cors());
app.use(express.json({ limit: "20mb" }));

// Frontend
app.use(express.static(path.join(__dirname, "frontend")));

// Vérification serveur
app.get("/api/health", (req, res) => {
  res.json({
    success: true,
    name: "Vox-GPT",
    version: "8.0.0",
    status: "online"
  });
});

// Chat IA
app.post("/api/chat", async (req, res) => {
  try {
    const { message } = req.body;

    if (!message || !message.trim()) {
      return res.status(400).json({
        success: false,
        error: "Message manquant."
      });
    }

    if (!process.env.OPENAI_API_KEY) {
      return res.status(500).json({
        success: false,
        error: "La clé API OpenAI n'est pas configurée."
      });
    }

    const response = await client.responses.create({
      model: "gpt-5.6-luna",
      instructions: `
Tu es Vox-GPT 8.0.0,
l'assistant IA de GEE VOX TECH.

Réponds en français par défaut.
Sois précis, clair, utile et professionnel.

Tu peux aider dans :
- Intelligence et conversation
- Recherche
- Programmation
- Documents
- Vision
- Création
- Voix et audio
- Données
- Agents et automatisation
- Connexions et API
- Sécurité
- Mémoire et personnalisation
- Applications
- Architecture

Ne prétends jamais avoir effectué une action
que tu n'as pas réellement effectuée.
`,
      input: message.trim()
    });

    res.json({
      success: true,
      name: "Vox-GPT",
      version: "8.0.0",
      reply: response.output_text
    });

  } catch (error) {
    console.error("Erreur API :", error);

    res.status(500).json({
      success: false,
      error: error.message || "Erreur serveur."
    });
  }
});

// Toutes les autres routes → frontend
app.get("*", (req, res) => {
  res.sendFile(
    path.join(__dirname, "frontend", "index.html")
  );
});

app.listen(PORT, "0.0.0.0", () => {
  console.log("================================");
  console.log("       VOX-GPT 8.0.0");
  console.log("       GEE VOX TECH");
  console.log("================================");
  console.log(`Serveur sur le port ${PORT}`);
});
<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>Vox-GPT 8.0.0 — GEE VOX TECH</title>

<style>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  background: #050505;
  color: #fff;
  font-family: Arial, sans-serif;
  min-height: 100vh;
}

header {
  padding: 22px;
  text-align: center;
  border-bottom: 1px solid #00ff4133;
  background: #080808;
}

.logo {
  color: #00ff41;
  font-size: 28px;
  font-weight: bold;
  text-shadow: 0 0 15px #00ff41;
}

.version {
  color: #888;
  margin-top: 5px;
}

.container {
  width: 95%;
  max-width: 1100px;
  margin: 25px auto;
}

.modules {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
  gap: 12px;
  margin-bottom: 25px;
}

.module {
  padding: 16px 10px;
  border: 1px solid #00ff4144;
  border-radius: 12px;
  background: #0d0d0d;
  text-align: center;
  cursor: pointer;
  transition: .2s;
}

.module:hover {
  border-color: #00ff41;
  box-shadow: 0 0 15px #00ff4133;
  transform: translateY(-2px);
}

.module span {
  display: block;
  font-size: 25px;
  margin-bottom: 8px;
}

.module small {
  color: #ddd;
}

.chat {
  background: #0b0b0b;
  border: 1px solid #00ff4133;
  border-radius: 16px;
  overflow: hidden;
}

.chat-title {
  padding: 16px;
  border-bottom: 1px solid #222;
  color: #00ff41;
  font-weight: bold;
}

.messages {
  height: 400px;
  overflow-y: auto;
  padding: 18px;
}

.message {
  margin-bottom: 15px;
  padding: 12px 15px;
  border-radius: 12px;
  max-width: 85%;
  white-space: pre-wrap;
  line-height: 1.5;
}

.user {
  margin-left: auto;
  background: #003d16;
  border: 1px solid #00ff4144;
}

.ai {
  background: #151515;
  border: 1px solid #292929;
}

.input-area {
  display: flex;
  gap: 10px;
  padding: 15px;
  border-top: 1px solid #222;
}

textarea {
  flex: 1;
  resize: none;
  min-height: 55px;
  padding: 15px;
  background: #050505;
  color: white;
  border: 1px solid #333;
  border-radius: 10px;
  outline: none;
}

textarea:focus {
  border-color: #00ff41;
}

button {
  border: none;
  border-radius: 10px;
  padding: 0 22px;
  background: #00ff41;
  color: #000;
  font-weight: bold;
  cursor: pointer;
}

button:hover {
  box-shadow: 0 0 15px #00ff41;
}

.status {
  text-align: center;
  margin-top: 18px;
  color: #777;
  font-size: 13px;
}

.online {
  color: #00ff41;
}

footer {
  text-align: center;
  padding: 30px;
  color: #555;
}
</style>
</head>

<body>

<header>
  <div class="logo">⚡ VOX-GPT</div>
  <div class="version">GEE VOX TECH • Version 8.0.0</div>
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

    <div class="module" onclick="moduleClick('Voix / Audio')">
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
        👋 Bonjour ! Je suis <b>Vox-GPT 8.0.0</b>,
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

      <button onclick="sendMessage()">
        Envoyer
      </button>
    </div>

  </div>

  <div id="status" class="status">
    Vérification du serveur...
  </div>

</div>

<footer>
  © 2026 GEE VOX TECH — VOX-GPT 8.0.0
</footer>

<script>

const messages = document.getElementById("messages");
const input = document.getElementById("message");
const status = document.getElementById("status");

function addMessage(text, type) {

  const div = document.createElement("div");

  div.className = "message " + type;

  div.textContent = text;

  messages.appendChild(div);

  messages.scrollTop = messages.scrollHeight;
}

async function sendMessage() {

  const text = input.value.trim();

  if (!text) return;

  addMessage(text, "user");

  input.value = "";

  const loading = document.createElement("div");

  loading.className = "message ai";
  loading.textContent = "Vox-GPT réfléchit...";

  messages.appendChild(loading);

  messages.scrollTop = messages.scrollHeight;

  try {

    const response = await fetch("/api/chat", {

      method: "POST",

      headers: {
        "Content-Type": "application/json"
      },

      body: JSON.stringify({
        message: text
      })

    });

    const data = await response.json();

    loading.remove();

    if (!data.success) {

      addMessage(
        "❌ Erreur : " + (data.error || "Erreur inconnue"),
        "ai"
      );

      return;
    }

    addMessage(data.reply, "ai");

  } catch (error) {

    loading.remove();

    addMessage(
      "❌ Impossible de contacter Vox-GPT. Vérifie le serveur.",
      "ai"
    );

  }
}

function handleKey(event) {

  if (event.key === "Enter" && !event.shiftKey) {

    event.preventDefault();

    sendMessage();

  }
}

function moduleClick(name) {

  input.value =
    "Active le module " + name +
    " et explique-moi ce que tu peux faire avec.";

  input.focus();

}

async function checkServer() {

  try {

    const response = await fetch("/api/health");

    const data = await response.json();

    if (data.success) {

      status.innerHTML =
        "● <span class='online'>Vox-GPT est en ligne</span>";

    }

  } catch {

    status.textContent =
      "● Serveur non disponible";

  }

}

checkServer();

</script>

</body>
</html>
