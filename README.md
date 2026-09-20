<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Vox-GPT ⚡</title>
<meta name="theme-color" content="#00f0ff">
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@600;800&family=Inter:wght@400;500;600&family=JetBrains+Mono&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/dompurify@3/dist/purify.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/highlight.js@11/lib/common.min.js"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/highlight.js@11/styles/atom-one-dark.min.css">
<script src="https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.min.js"></script>

<style>
* { margin: 0; padding: 0; box-sizing: border-box; }

:root {
  --bg: #05050a; --bg-2: #0b0b17; --panel: #0f0f1e; --border: #1e1e3a;
  --cyan: #00f0ff; --magenta: #ff00e5; --violet: #8a2be2;
  --text: #e6e6ff; --text-dim: #6c6c9c;
}
[data-theme="dark"] { --bg:#1a1a1a;--bg-2:#222;--panel:#181818;--border:#2e2e2e;--cyan:#10a37f;--magenta:#10a37f;--violet:#10a37f;--text:#ececec;--text-dim:#888; }
[data-theme="light"] { --bg:#f7f7f8;--bg-2:#fff;--panel:#fff;--border:#e0e0e0;--cyan:#7c3aed;--magenta:#db2777;--violet:#7c3aed;--text:#111;--text-dim:#666; }

body { background: var(--bg); color: var(--text); font-family: 'Inter', sans-serif; height: 100vh; overflow: hidden; transition: background .3s; }

body::before {
  content: ""; position: fixed; inset: 0;
  background:
    radial-gradient(circle at 15% 20%, rgba(0,240,255,0.08), transparent 40%),
    radial-gradient(circle at 85% 80%, rgba(255,0,229,0.08), transparent 40%);
  pointer-events: none; z-index: 0;
}
[data-theme="dark"] body::before, [data-theme="light"] body::before { display: none; }

.app { display: flex; height: 100vh; position: relative; z-index: 1; }

/* ============ LOGIN ============ */
#loginScreen {
  position: fixed; inset: 0; z-index: 200;
  display: flex; align-items: center; justify-content: center;
  background: var(--bg);
}
.login-box {
  background: var(--panel); border: 1px solid var(--border);
  border-radius: 16px; padding: 40px; width: 90%; max-width: 400px;
  box-shadow: 0 0 40px rgba(0,240,255,0.2); text-align: center;
}
.login-box h1 {
  font-family: 'Orbitron', sans-serif; color: var(--cyan);
  margin-bottom: 25px; font-size: 26px; text-shadow: 0 0 12px var(--cyan);
}
.login-box input {
  width: 100%; padding: 14px; margin-bottom: 12px;
  background: var(--bg-2); border: 1px solid var(--border);
  border-radius: 10px; color: var(--text); font-size: 15px; font-family: inherit;
}
.login-box input:focus { outline: none; border-color: var(--cyan); }
.login-box button {
  width: 100%; padding: 14px; margin-top: 6px;
  background: linear-gradient(135deg, var(--cyan), var(--magenta));
  border: none; border-radius: 10px; color: #000;
  font-weight: 700; font-size: 15px; cursor: pointer; transition: .25s; font-family: inherit;
}
.login-box button:hover { transform: scale(1.02); box-shadow: 0 0 25px rgba(255,0,229,.6); }
.toggle { margin-top: 16px; font-size: 13px; color: var(--text-dim); }
.toggle a { color: var(--cyan); cursor: pointer; text-decoration: none; }
.error { color: var(--magenta); margin-top: 12px; font-size: 13px; }

/* ============ SIDEBAR ============ */
.sidebar {
  width: 270px; background: var(--panel); border-right: 1px solid var(--border);
  display: flex; flex-direction: column; padding: 20px 15px; gap: 14px;
}
.logo {
  font-family: 'Orbitron', sans-serif; font-size: 22px; font-weight: 800;
  letter-spacing: 1px; color: var(--cyan);
  text-shadow: 0 0 8px var(--cyan), 0 0 20px rgba(0,240,255,.5);
  margin-bottom: 10px;
}
.logo span { color: var(--magenta); text-shadow: 0 0 8px var(--magenta); }
[data-theme="dark"] .logo, [data-theme="light"] .logo,
[data-theme="dark"] .logo span, [data-theme="light"] .logo span { text-shadow: none; }

.new-chat {
  background: transparent; border: 1px solid var(--cyan); color: var(--cyan);
  padding: 12px; border-radius: 10px; cursor: pointer;
  text-align: left; font-weight: 500; transition: .25s;
}
.new-chat:hover { background: rgba(0,240,255,.08); box-shadow: 0 0 15px rgba(0,240,255,.5); }

.history-title { color: var(--text-dim); font-size: 11px; text-transform: uppercase; letter-spacing: 1.5px; margin-top: 10px; }
.history { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 4px; }
.history-item {
  padding: 9px 12px; border-radius: 8px; font-size: 13px;
  color: var(--text-dim); cursor: pointer;
  white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
  transition: .2s; border-left: 2px solid transparent;
  display: flex; justify-content: space-between; align-items: center; gap: 6px;
}
.history-item:hover { background: rgba(138,43,226,.15); color: var(--text); border-left-color: var(--violet); }
.history-item.active { background: rgba(0,240,255,.1); color: var(--cyan); border-left-color: var(--cyan); }
.history-item .del {
  opacity: 0; font-size: 12px; color: var(--magenta); padding: 2px 4px;
  cursor: pointer; transition: .2s;
}
.history-item:hover .del { opacity: 1; }

.sidebar-footer { margin-top: auto; display: flex; flex-direction: column; gap: 8px; }
.label { font-size: 11px; color: var(--text-dim); text-transform: uppercase; letter-spacing: 1.5px; }
.sidebar-footer select, .settings-btn {
  width: 100%; background: var(--bg-2); color: var(--text);
  border: 1px solid var(--border); padding: 10px;
  border-radius: 8px; cursor: pointer; outline: none;
  font-family: inherit; font-size: 13px;
}
.settings-btn { transition: .2s; }
.settings-btn:hover { border-color: var(--cyan); color: var(--cyan); }

/* ============ CHAT ============ */
.chat { flex: 1; display: flex; flex-direction: column; min-width: 0; }
.topbar {
  display: flex; justify-content: flex-end; align-items: center;
  gap: 12px; padding: 12px 30px; border-bottom: 1px solid var(--border);
}
.tokens { font-size: 12px; color: var(--text-dim); font-family: 'JetBrains Mono', monospace; }
.icon-btn {
  background: transparent; border: 1px solid var(--border); color: var(--text-dim);
  width: 36px; height: 36px; border-radius: 8px; cursor: pointer;
  font-size: 15px; transition: .2s;
}
.icon-btn:hover { border-color: var(--cyan); color: var(--cyan); }
.icon-btn.stop { background: rgba(255,0,229,.15); border-color: var(--magenta); color: var(--magenta); }

.messages {
  flex: 1; overflow-y: auto; padding: 40px 15%;
  display: flex; flex-direction: column; gap: 18px; scroll-behavior: smooth;
}
.welcome { text-align: center; margin-top: 12vh; color: var(--text-dim); }
.welcome h1 { font-family: 'Orbitron', sans-serif; font-size: 34px; margin-bottom: 12px; color: var(--text); }
.glow { color: var(--cyan); text-shadow: 0 0 10px var(--cyan), 0 0 30px rgba(0,240,255,.6); }
[data-theme="dark"] .glow, [data-theme="light"] .glow { text-shadow: none; }

.suggestions { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; margin-top: 30px; }
.suggestions button {
  background: var(--bg-2); border: 1px solid var(--border); color: var(--text-dim);
  padding: 10px 16px; border-radius: 20px; cursor: pointer;
  font-size: 13px; transition: .2s;
}
.suggestions button:hover { border-color: var(--cyan); color: var(--cyan); box-shadow: 0 0 12px rgba(0,240,255,.3); }

.msg {
  padding: 14px 18px; border-radius: 14px; max-width: 82%;
  line-height: 1.65; animation: fade .25s ease; word-wrap: break-word;
}
@keyframes fade { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; } }
.msg.user {
  align-self: flex-end;
  background: linear-gradient(135deg, rgba(255,0,229,.15), rgba(138,43,226,.15));
  border: 1px solid rgba(255,0,229,.4); color: var(--text);
}
.msg.bot {
  align-self: flex-start;
  background: rgba(0,240,255,.05); border: 1px solid rgba(0,240,255,.25);
  color: var(--text); width: fit-content; min-width: 60px;
}
.msg.bot::before { content: "⚡"; margin-right: 8px; color: var(--cyan); text-shadow: 0 0 8px var(--cyan); }

.msg p { margin: 0 0 10px; } .msg p:last-child { margin-bottom: 0; }
.msg ul, .msg ol { margin: 8px 0 8px 22px; } .msg li { margin: 4px 0; }
.msg code:not(pre code) {
  background: rgba(0,240,255,.12); padding: 2px 6px; border-radius: 4px;
  font-family: 'JetBrains Mono', monospace; font-size: .9em;
}
.msg pre {
  background: #0a0a14; border: 1px solid var(--border); border-radius: 10px;
  padding: 14px; margin: 10px 0; overflow-x: auto; position: relative;
}
.msg pre code { font-family: 'JetBrains Mono', monospace; font-size: 13px; }
.copy-code {
  position: absolute; top: 8px; right: 8px;
  background: var(--panel); border: 1px solid var(--border); color: var(--text-dim);
  padding: 4px 10px; border-radius: 6px; font-size: 11px; cursor: pointer;
}
.copy-code:hover { color: var(--cyan); border-color: var(--cyan); }

.msg-actions { display: flex; gap: 8px; margin-top: 10px; opacity: 0; transition: .2s; flex-wrap: wrap; }
.msg.bot:hover .msg-actions { opacity: 1; }
.msg-actions button {
  background: transparent; border: 1px solid var(--border); color: var(--text-dim);
  font-size: 11px; padding: 4px 10px; border-radius: 6px; cursor: pointer; transition: .2s;
}
.msg-actions button:hover { color: var(--cyan); border-color: var(--cyan); }

.typing::after { content: "▋"; color: var(--cyan); animation: blink 1s infinite; margin-left: 2px; }
@keyframes blink { 50% { opacity: 0; } }

/* ============ INPUT ============ */
.input-wrapper { padding: 18px 15%; background: var(--bg); border-top: 1px solid var(--border); }
.image-preview { position: relative; width: fit-content; margin-bottom: 10px; }
.image-preview img { max-height: 80px; border-radius: 8px; border: 1px solid var(--border); }
.image-preview button {
  position: absolute; top: -8px; right: -8px;
  background: var(--magenta); border: none; color: white;
  width: 22px; height: 22px; border-radius: 50%; cursor: pointer;
}
.input-bar {
  display: flex; gap: 8px; align-items: flex-end;
  background: var(--bg-2); border: 1px solid var(--border);
  border-radius: 14px; padding: 8px; transition: .2s;
}
.input-bar:focus-within { border-color: var(--cyan); box-shadow: 0 0 20px rgba(0,240,255,.25); }
textarea {
  flex: 1; background: transparent; border: none; padding: 10px 6px;
  color: var(--text); font-family: inherit; font-size: 15px;
  resize: none; max-height: 150px; outline: none;
}
#sendBtn {
  background: linear-gradient(135deg, var(--cyan), var(--magenta));
  border: none; color: #000; width: 40px; height: 40px; border-radius: 10px;
  cursor: pointer; font-size: 16px; font-weight: bold; transition: .25s;
}
#sendBtn:hover { transform: scale(1.05); box-shadow: 0 0 20px rgba(255,0,229,.6); }
#sendBtn:disabled { opacity: .4; cursor: not-allowed; transform: none; }

/* ============ MODAL ============ */
.modal {
  position: fixed; inset: 0; background: rgba(0,0,0,.7);
  display: flex; align-items: center; justify-content: center;
  z-index: 100; backdrop-filter: blur(4px);
}
.modal-content {
  background: var(--panel); border: 1px solid var(--border);
  border-radius: 16px; padding: 26px; width: 90%; max-width: 500px;
  box-shadow: 0 0 40px rgba(0,240,255,.2);
}
.modal-content h2 { margin-bottom: 12px; color: var(--cyan); font-family: 'Orbitron', sans-serif; }
.hint { color: var(--text-dim); font-size: 13px; margin-bottom: 14px; }
.modal-content textarea {
  width: 100%; background: var(--bg-2); border: 1px solid var(--border);
  border-radius: 10px; padding: 12px; color: var(--text);
  font-family: inherit; font-size: 14px; resize: vertical;
}
.modal-content textarea:focus { outline: none; border-color: var(--cyan); }
.modal-content input[type="password"] {
  width: 100%; background: var(--bg-2); border: 1px solid var(--border);
  border-radius: 10px; padding: 12px; color: var(--text); font-family: inherit; margin-bottom: 8px;
}
.modal-actions { display: flex; justify-content: flex-end; gap: 10px; margin-top: 16px; }
.modal-actions button {
  padding: 10px 18px; border-radius: 8px;
  border: 1px solid var(--border); background: transparent;
  color: var(--text); cursor: pointer; font-family: inherit;
}
.modal-actions .primary {
  background: linear-gradient(135deg, var(--cyan), var(--magenta));
  border: none; color: #000; font-weight: 600;
}
.shortcuts { list-style: none; display: flex; flex-direction: column; gap: 8px; }
.shortcuts li { display: flex; gap: 10px; align-items: center; color: var(--text-dim); }
kbd {
  background: var(--bg-2); border: 1px solid var(--border);
  padding: 3px 8px; border-radius: 5px;
  font-family: 'JetBrains Mono', monospace; font-size: 12px; color: var(--cyan);
}
.doc-item {
  display: flex; justify-content: space-between; align-items: center;
  padding: 10px 12px; background: var(--bg-2); border: 1px solid var(--border);
  border-radius: 8px; margin-bottom: 6px; font-size: 13px;
}
.doc-item button {
  background: transparent; border: none; color: var(--magenta); cursor: pointer; font-size: 14px;
}

/* ============ SCROLLBAR ============ */
::-webkit-scrollbar { width: 8px; }
::-webkit-scrollbar-track { background: var(--bg); }
::-webkit-scrollbar-thumb { background: linear-gradient(var(--cyan), var(--magenta)); border-radius: 4px; }

/* ============ MOBILE ============ */
@media (max-width: 768px) {
  .sidebar { display: none; }
  .messages, .input-wrapper { padding: 15px; }
  .welcome h1 { font-size: 24px; }
}
</style>
</head>
<body data-theme="neon">

<!-- ============ LOGIN SCREEN ============ -->
<div id="loginScreen">
  <div class="login-box">
    <h1>⚡ Vox-GPT</h1>
    <input id="loginEmail" type="email" placeholder="Email" autocomplete="email">
    <input id="loginPassword" type="password" placeholder="Mot de passe" autocomplete="current-password">
    <button id="loginBtn" onclick="submitAuth()">Se connecter</button>
    <div class="toggle">
      <span id="toggleText">Pas de compte ?</span>
      <a onclick="toggleAuthMode()" id="toggleLink">Créer un compte</a>
    </div>
    <div id="loginError" class="error"></div>
  </div>
</div>

<!-- ============ APP ============ -->
<div class="app" id="app" style="display:none;">
  <aside class="sidebar">
    <div class="logo">⚡ Vox<span>GPT</span></div>
    <button class="new-chat" onclick="resetChat()">＋ Nouvelle conversation</button>

    <div class="history-title">Historique</div>
    <div id="history" class="history"></div>

    <div class="sidebar-footer">
      <label class="label">Modèle</label>
      <select id="provider">
        <option value="gemini">✨ Gemini Flash</option>
        <option value="openai">🧠 GPT-4o mini</option>
      </select>

      <label class="label">Thème</label>
      <select id="theme">
        <option value="neon">🌌 Neon</option>
        <option value="dark">🌑 Sombre</option>
        <option value="light">☀️ Clair</option>
      </select>

      <button class="settings-btn" onclick="openSettings()">⚙️ Prompt système</button>
      <button class="settings-btn" onclick="openApiKeys()">🔑 Clés API</button>
      <button class="settings-btn" onclick="openDocs()">📄 Documents</button>
      <button class="settings-btn" onclick="logout()">🚪 Déconnexion</button>
    </div>
  </aside>

  <main class="chat">
    <header class="topbar">
      <div id="userEmail" class="tokens"></div>
      <div id="tokenCount" class="tokens">~0 tokens</div>
      <button class="icon-btn" onclick="exportChat()" title="Exporter">⬇️</button>
    </header>

    <div id="messages" class="messages">
      <div class="welcome">
        <h1>Bienvenue sur <span class="glow">Vox-GPT</span></h1>
        <p>Assistant IA nouvelle génération ⚡</p>
        <div class="suggestions">
          <button onclick="quickAsk('Explique-moi les trous noirs simplement')">🌌 Trous noirs</button>
          <button onclick="quickAsk('/search actualité IA 2025')">🔍 Recherche web</button>
          <button onclick="quickAsk('/py print([x**2 for x in range(10)])')">🐍 Python</button>
          <button onclick="quickAsk('12 * (34 + 56) / 2')">🧮 Calcul</button>
        </div>
      </div>
    </div>

    <div class="input-wrapper">
      <div id="preview" class="image-preview" style="display:none;">
        <img id="previewImg">
        <button onclick="clearImage()">✕</button>
      </div>

      <div class="input-bar">
        <button class="icon-btn" onclick="document.getElementById('fileInput').click()" title="Image">📎</button>
        <input type="file" id="fileInput" accept="image/*" hidden onchange="handleImage(event)">

        <button class="icon-btn" onclick="document.getElementById('pdfInput').click()" title="PDF">📄</button>
        <input type="file" id="pdfInput" accept=".pdf,.txt,.md" hidden onchange="handlePdf(event)">

        <button class="icon-btn" id="micBtn" onclick="toggleMic()" title="Micro">🎤</button>

        <textarea id="input" placeholder="Écris ton message... (essaie /search, /py, ou un calcul)" rows="1"></textarea>

        <button id="stopBtn" class="icon-btn stop" onclick="stopGeneration()" style="display:none;">⏹</button>
        <button onclick="send()" id="sendBtn">➤</button>
      </div>
    </div>
  </main>
</div>

<!-- ============ MODALS ============ -->
<div id="settingsModal" class="modal" style="display:none;">
  <div class="modal-content">
    <h2>⚙️ Prompt système</h2>
    <p class="hint">Définis le comportement de Vox-GPT.</p>
    <textarea id="systemPrompt" rows="6"></textarea>
    <div class="modal-actions">
      <button onclick="closeModal('settingsModal')">Annuler</button>
      <button class="primary" onclick="saveSettings()">Enregistrer</button>
    </div>
  </div>
</div>

<div id="apiModal" class="modal" style="display:none;">
  <div class="modal-content">
    <h2>🔑 Clés API</h2>
    <p class="hint">Stockées dans ton navigateur (localStorage). Ne partage jamais ton lien/PC.</p>
    <label class="label">Gemini API Key</label>
    <input type="password" id="geminiKey" placeholder="AIza...">
    <label class="label">OpenAI API Key (optionnel)</label>
    <input type="password" id="openaiKey" placeholder="sk-...">
    <div class="modal-actions">
      <button onclick="closeModal('apiModal')">Annuler</button>
      <button class="primary" onclick="saveApiKeys()">Enregistrer</button>
    </div>
  </div>
</div>

<div id="docsModal" class="modal" style="display:none;">
  <div class="modal-content">
    <h2>📄 Documents (RAG)</h2>
    <p class="hint">Ces documents sont envoyés à l'IA pour répondre à tes questions.</p>
    <div id="docsList"></div>
    <div class="modal-actions">
      <button class="primary" onclick="closeModal('docsModal')">OK</button>
    </div>
  </div>
</div>

<script>
/* ============================================================
   VOX-GPT — SPA complète (tout en un)
   1. Auth localStorage  2. Historique  3. RAG PDF
   4. Recherche web      5. Calcul      6. Python runner
   7. Gemini/OpenAI + streaming + vocal + vision + PWA
============================================================ */

/* ============ STATE ============ */
let currentSession = newSessionId();
let currentMessages = [];
let lastUserMessage = "";
let currentImage = null;
let abortController = null;
let recognition = null, isRecording = false;
let authMode = "login";
let currentUser = null;

const $ = (id) => document.getElementById(id);
const messagesEl = $("messages");
const input = $("input");
const sendBtn = $("sendBtn");
const stopBtn = $("stopBtn");

/* ============ UTILS ============ */
function newSessionId() { return "vox-" + Date.now() + "-" + Math.random().toString(36).slice(2, 8); }
function escapeHtml(s) {
  return String(s).replace(/&/g,"&amp;").replace(/</g,"&lt;").replace(/>/g,"&gt;");
}
function renderMarkdown(text) {
  const raw = marked.parse(text || "");
  const clean = DOMPurify.sanitize(raw);
  const temp = document.createElement("div");
  temp.innerHTML = clean;
  temp.querySelectorAll("pre code").forEach((block) => {
    try { hljs.highlightElement(block); } catch(_) {}
    const pre = block.parentElement;
    if (!pre.querySelector(".copy-code")) {
      const btn = document.createElement("button");
      btn.className = "copy-code";
      btn.textContent = "📋 Copier";
      btn.onclick = () => {
        navigator.clipboard.writeText(block.textContent);
        btn.textContent = "✅";
        setTimeout(() => (btn.textContent = "📋 Copier"), 1200);
      };
      pre.appendChild(btn);
    }
  });
  return temp.innerHTML;
}

/* ============ AUTH ============ */
function loadUsers() { return JSON.parse(localStorage.getItem("vox_users") || "[]"); }
function saveUsers(u) { localStorage.setItem("vox_users", JSON.stringify(u)); }

async function hashPw(pw) {
  const enc = new TextEncoder().encode(pw + "vox-salt");
  const buf = await crypto.subtle.digest("SHA-256", enc);
  return Array.from(new Uint8Array(buf)).map(b => b.toString(16).padStart(2,"0")).join("");
}

function toggleAuthMode() {
  authMode = authMode === "login" ? "register" : "login";
  $("loginBtn").textContent = authMode === "login" ? "Se connecter" : "Créer le compte";
  $("toggleText").textContent = authMode === "login" ? "Pas de compte ?" : "Déjà un compte ?";
  $("toggleLink").textContent = authMode === "login" ? "Créer un compte" : "Se connecter";
  $("loginError").textContent = "";
}

async function submitAuth() {
  const email = $("loginEmail").value.trim().toLowerCase();
  const pw = $("loginPassword").value;
  const err = $("loginError");
  err.textContent = "";
  if (!email || !pw) { err.textContent = "Remplis tous les champs."; return; }
  if (pw.length < 4) { err.textContent = "Mot de passe trop court."; return; }

  const users = loadUsers();
  const hash = await hashPw(pw);

  if (authMode === "register") {
    if (users.find(u => u.email === email)) { err.textContent = "Email déjà utilisé."; return; }
    users.push({ email, hash, id: crypto.randomUUID() });
    saveUsers(users);
  } else {
    const u = users.find(x => x.email === email && x.hash === hash);
    if (!u) { err.textContent = "Identifiants invalides."; return; }
  }

  currentUser = { email };
  localStorage.setItem("vox_current_user", email);
  showApp();
}

function showApp() {
  $("loginScreen").style.display = "none";
  $("app").style.display = "flex";
  $("userEmail").textContent = currentUser.email;
  renderHistory();
  updateTokens();
  input.focus();
}

function logout() {
  localStorage.removeItem("vox_current_user");
  currentUser = null;
  $("loginScreen").style.display = "flex";
  $("app").style.display = "none";
  $("loginPassword").value = "";
}

/* ============ HISTORIQUE ============ */
function userKey(k) { return `vox_${currentUser.email}_${k}`; }

function loadHistory() { return JSON.parse(localStorage.getItem(userKey("history")) || "[]"); }
function saveHistory(h) { localStorage.setItem(userKey("history"), JSON.stringify(h)); }

function renderHistory() {
  const box = $("history");
  const hist = loadHistory();
  box.innerHTML = "";
  hist.slice().reverse().forEach((s) => {
    const div = document.createElement("div");
    div.className = "history-item" + (s.id === currentSession ? " active" : "");
    const span = document.createElement("span");
    span.textContent = s.title || "Conversation";
    span.style.flex = "1"; span.style.overflow = "hidden"; span.style.textOverflow = "ellipsis";
    const del = document.createElement("span");
    del.className = "del"; del.textContent = "✕";
    del.onclick = (e) => { e.stopPropagation(); deleteSession(s.id); };
    div.appendChild(span); div.appendChild(del);
    div.onclick = () => loadSession(s.id);
    box.appendChild(div);
  });
}

function upsertSession(title) {
  const hist = loadHistory();
  const existing = hist.find(s => s.id === currentSession);
  if (existing) {
    existing.title = title;
    existing.messages = currentMessages;
    existing.updated = Date.now();
  } else {
    hist.push({ id: currentSession, title, messages: currentMessages, updated: Date.now() });
  }
  saveHistory(hist);
  renderHistory();
}

function loadSession(id) {
  const s = loadHistory().find(x => x.id === id);
  if (!s) return;
  currentSession = id;
  currentMessages = s.messages || [];
  messagesEl.innerHTML = "";
  currentMessages.forEach((m) => {
    addMessage(m.content, m.role === "user" ? "user" : "bot", m.role !== "user", false);
  });
  renderHistory();
  updateTokens();
}

function deleteSession(id) {
  const hist = loadHistory().filter(s => s.id !== id);
  saveHistory(hist);
  if (id === currentSession) resetChat();
  else renderHistory();
}

/* ============ OUTILS INTERNES ============ */
// 1. CALCULATRICE
function calculator(expr) {
  if (!/^[\d\s\+\-\*\/\(\)\.\,\%]+$/.test(expr)) return "Expression invalide";
  try {
    const safe = expr.replace(/,/g, ".");
    const result = Function('"use strict"; return (' + safe + ')')();
    return String(result);
  } catch (e) { return "Erreur calcul : " + e.message; }
}

// 2. RECHERCHE WEB (DuckDuckGo Instant Answer + HTML via proxy)
async function webSearch(query) {
  try {
    // DuckDuckGo Instant Answer API (pas de CORS, JSON direct)
    const res = await fetch(`https://api.duckduckgo.com/?q=${encodeURIComponent(query)}&format=json&no_html=1&skip_disambig=1`);
    const data = await res.json();
    const parts = [];
    if (data.AbstractText) parts.push(`📖 ${data.AbstractText}\n${data.AbstractURL || ""}`);
    if (data.Answer) parts.push(`✅ ${data.Answer}`);
    if (data.RelatedTopics?.length) {
      data.RelatedTopics.slice(0, 4).forEach(t => {
        if (t.Text) parts.push(`• ${t.Text}${t.FirstURL ? "\n  " + t.FirstURL : ""}`);
      });
    }
    return parts.join("\n\n") || "Aucun résultat trouvé pour « " + query + " ». Essaie d'être plus précis.";
  } catch (e) {
    return "Erreur recherche : " + e.message;
  }
}

// 3. PYTHON RUNNER (via Piston API publique)
async function runPython(code) {
  try {
    const res = await fetch("https://emkc.org/api/v2/piston/execute", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        language: "python", version: "3.10.0",
        files: [{ content: code }],
      }),
    });
    const data = await res.json();
    const out = data.run?.stdout || "";
    const err = data.run?.stderr || "";
    return (out + (err ? "\n[stderr]\n" + err : "")).trim() || "(aucune sortie)";
  } catch (e) { return "Erreur exécution : " + e.message; }
}

// 4. RAG — recherche dans documents
function chunkText(text, size = 800, overlap = 100) {
  const words = text.split(/\s+/);
  const chunks = []; let cur = [], len = 0;
  for (const w of words) {
    cur.push(w); len += w.length + 1;
    if (len >= size) { chunks.push(cur.join(" ")); cur = cur.slice(-20); len = cur.join(" ").length; }
  }
  if (cur.length) chunks.push(cur.join(" "));
  return chunks;
}

function retrieveContext(query, docs, topK = 3) {
  const qWords = new Set(query.toLowerCase().match(/\w+/g) || []);
  if (!qWords.size) return "";
  const all = [];
  docs.forEach(d => {
    chunkText(d.content).forEach(ch => {
      const cWords = new Set(ch.toLowerCase().match(/\w+/g) || []);
      let inter = 0; qWords.forEach(w => cWords.has(w) && inter++);
      const score = inter / qWords.size;
      all.push({ score, text: ch, filename: d.filename });
    });
  });
  all.sort((a, b) => b.score - a.score);
  const top = all.slice(0, topK).filter(c => c.score > 0);
  return top.map(c => `[${c.filename}]\n${c.text}`).join("\n\n---\n\n");
}

// 5. DETECTION D'OUTILS
function detectTool(msg) {
  const m = msg.trim();
  if (/^[\d\s\+\-\*\/\(\)\.\,\%]+$/.test(m) && /[\+\-\*\/\%]/.test(m)) return { name: "calc", arg: m };
  if (/^\/(search|recherche|cherche)\s+/i.test(m)) return { name: "search", arg: m.replace(/^\/\S+\s+/, "") };
  if (/^```python/i.test(m)) return { name: "python", arg: m.replace(/```python/i, "").replace(/```/g, "").trim() };
  if (/^\/py\s+/i.test(m)) return { name: "python", arg: m.replace(/^\/py\s+/i, "") };
  return null;
}

/* ============ DOCUMENTS RAG ============ */
function loadDocs() { return JSON.parse(localStorage.getItem(userKey("docs_" + currentSession)) || "[]"); }
function saveDocs(d) { localStorage.setItem(userKey("docs_" + currentSession), JSON.stringify(d)); }

async function handlePdf(e) {
  const file = e.target.files[0]; if (!file) return;
  const name = file.name;

  if (name.toLowerCase().endsWith(".pdf")) {
    const buf = await file.arrayBuffer();
    const pdf = await pdfjsLib.getDocument({ data: buf }).promise;
    let text = "";
    for (let i = 1; i <= pdf.numPages; i++) {
      const page = await pdf.getPage(i);
      const content = await page.getTextContent();
      text += content.items.map(it => it.str).join(" ") + "\n";
    }
    const docs = loadDocs();
    docs.push({ filename: name, content: text });
    saveDocs(docs);
    addSystemMsg(`📄 Document « ${name} » ajouté (${text.length} caractères, ${pdf.numPages} pages). L'IA peut maintenant répondre dessus.`);
  } else {
    const text = await file.text();
    const docs = loadDocs();
    docs.push({ filename: name, content: text });
    saveDocs(docs);
    addSystemMsg(`📄 Fichier « ${name} » ajouté (${text.length} caractères).`);
  }
  e.target.value = "";
}

function addSystemMsg(txt) {
  const welcome = document.querySelector(".welcome");
  if (welcome) welcome.remove();
  const div = document.createElement("div");
  div.className = "msg bot";
  div.innerHTML = renderMarkdown("ℹ️ " + txt);
  messagesEl.appendChild(div);
  messagesEl.scrollTop = messagesEl.scrollHeight;
}

function openDocs() {
  const list = $("docsList");
  const docs = loadDocs();
  list.innerHTML = docs.length ? "" : "<p class='hint'>Aucun document.</p>";
  docs.forEach((d, i) => {
    const div = document.createElement("div");
    div.className = "doc-item";
    div.innerHTML = `<span>📄 ${escapeHtml(d.filename)} (${d.content.length} car.)</span>`;
    const btn = document.createElement("button");
    btn.textContent = "🗑";
    btn.onclick = () => {
      const arr = loadDocs(); arr.splice(i, 1); saveDocs(arr); openDocs();
    };
    div.appendChild(btn);
    list.appendChild(div);
  });
  $("docsModal").style.display = "flex";
}

/* ============ API KEYS ============ */
function getKey(name) {
  return localStorage.getItem("vox_" + name) || "";
}
function saveApiKeys() {
  localStorage.setItem("vox_gemini", $("geminiKey").value.trim());
  localStorage.setItem("vox_openai", $("openaiKey").value.trim());
  closeModal("apiModal");
}
function openApiKeys() {
  $("geminiKey").value = getKey("gemini");
  $("openaiKey").value = getKey("openai");
  $("apiModal").style.display = "flex";
}

/* ============ IA APPELS ============ */
async function callGemini(history) {
  const key = getKey("gemini");
  if (!key) throw new Error("Clé Gemini manquante. Va dans 🔑 Clés API.");

  const system = history.find(m => m.role === "system")?.content || "";
  const contents = history.filter(m => m.role !== "system").map(m => ({
    role: m.role === "assistant" ? "model" : "user",
    parts: [{ text: typeof m.content === "string" ? m.content : JSON.stringify(m.content) }]
  }));

  const res = await fetch(
    `https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:streamGenerateContent?alt=sse&key=${key}`,
    {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        contents,
        systemInstruction: system ? { parts: [{ text: system }] } : undefined,
      }),
      signal: abortController.signal,
    }
  );

  if (!res.ok) {
    const txt = await res.text();
    throw new Error("Gemini : " + txt.slice(0, 200));
  }

  const reader = res.body.getReader();
  const decoder = new TextDecoder();
  let buffer = "";
  let full = "";

  while (true) {
    const { value, done } = await reader.read();
    if (done) break;
    buffer += decoder.decode(value, { stream: true });
    const lines = buffer.split("\n");
    buffer = lines.pop();
    for (const line of lines) {
      if (!line.startsWith("data: ")) continue;
      const json = line.slice(6).trim();
      if (!json || json === "[DONE]") continue;
      try {
        const parsed = JSON.parse(json);
        const chunk = parsed.candidates?.[0]?.content?.parts?.[0]?.text || "";
        if (chunk) { full += chunk; yieldChunk(chunk); }
      } catch (_) {}
    }
  }
  return full;
}

async function callOpenAI(history, image) {
  const key = getKey("openai");
  if (!key) throw new Error("Clé OpenAI manquante.");

  const messages = history.map(m => {
    if (m.role === "user" && image) {
      return {
        role: "user",
        content: [
          { type: "text", text: m.content },
          { type: "image_url", image_url: { url: image } }
        ]
      };
    }
    return m;
  });

  const res = await fetch("https://api.openai.com/v1/chat/completions", {
    method: "POST",
    headers: { "Content-Type": "application/json", "Authorization": "Bearer " + key },
    body: JSON.stringify({ model: "gpt-4o-mini", messages, stream: true }),
    signal: abortController.signal,
  });

  if (!res.ok) {
    const txt = await res.text();
    throw new Error("OpenAI : " + txt.slice(0, 200));
  }

  const reader = res.body.getReader();
  const decoder = new TextDecoder();
  let buffer = "", full = "";
  while (true) {
    const { value, done } = await reader.read();
    if (done) break;
    buffer += decoder.decode(value, { stream: true });
    const lines = buffer.split("\n");
    buffer = lines.pop();
    for (const line of lines) {
      if (!line.startsWith("data: ")) continue;
      const json = line.slice(6).trim();
      if (!json || json === "[DONE]") continue;
      try {
        const parsed = JSON.parse(json);
        const chunk = parsed.choices?.[0]?.delta?.content || "";
        if (chunk) { full += chunk; yieldChunk(chunk); }
      } catch (_) {}
    }
  }
  return full;
}

/* ============ STREAMING UI ============ */
let streamTarget = null;
let streamText = "";
function yieldChunk(chunk) {
  streamText += chunk;
  if (streamTarget) {
    streamTarget.innerHTML = renderMarkdown(streamText);
    messagesEl.scrollTop = messagesEl.scrollHeight;
  }
}

/* ============ ENVOI ============ */
async function send() {
  const text = input.value.trim();
  if (!text && !currentImage) return;

  const welcome = document.querySelector(".welcome");
  if (welcome) welcome.remove();

  addMessage(text || "🖼️ [image]", "user");
  currentMessages.push({ role: "user", content: text });
  lastUserMessage = text;

  const imgPayload = currentImage;
  clearImage();

  input.value = ""; input.style.height = "auto";
  sendBtn.style.display = "none"; stopBtn.style.display = "flex";

  const botDiv = addMessage("", "bot", false);
  botDiv.classList.add("typing");
  streamTarget = botDiv; streamText = "";
  abortController = new AbortController();

  let finalReply = "";

  try {
    // ---- Outils internes ----
    const tool = detectTool(text);
    if (tool) {
      let result = "";
      if (tool.name === "calc") result = `🧮 **${tool.arg}** = **${calculator(tool.arg)}**`;
      else if (tool.name === "search") {
        botDiv.innerHTML = renderMarkdown("🔍 Recherche en cours...");
        result = "🌐 **Résultats web :**\n\n" + await webSearch(tool.arg);
      } else if (tool.name === "python") {
        botDiv.innerHTML = renderMarkdown("🐍 Exécution...");
        result = "🐍 **Sortie Python :**\n\n```\n" + await runPython(tool.arg) + "\n```";
      }
      streamText = result;
      botDiv.innerHTML = renderMarkdown(result);
      finalReply = result;
    } else {
      // ---- IA ----
      const systemPrompt = localStorage.getItem(userKey("system")) || 
        "Tu es Vox-GPT, assistant IA moderne, clair et utile. Réponds en markdown.";

      // RAG : injecte docs
      const docs = loadDocs();
      let finalSystem = systemPrompt;
      if (docs.length) {
        const ctx = retrieveContext(text, docs);
        if (ctx) finalSystem += `\n\nContexte issu des documents de l'utilisateur :\n${ctx}`;
      }

      const history = [
        { role: "system", content: finalSystem },
        ...currentMessages.slice(-20),
      ];

      const provider = $("provider").value;
      if (provider === "gemini") {
        finalReply = await callGemini(history);
      } else {
        finalReply = await callOpenAI(history, imgPayload);
      }
    }
  } catch (e) {
    if (e.name !== "AbortError") {
      finalReply = "❌ " + e.message;
      botDiv.innerHTML = renderMarkdown(finalReply);
    }
  }

  botDiv.classList.remove("typing");
  botDiv.innerHTML = renderMarkdown(finalReply);
  addActions(botDiv, finalReply);
  if (finalReply) currentMessages.push({ role: "assistant", content: finalReply });

  sendBtn.style.display = "flex"; stopBtn.style.display = "none";
  abortController = null; streamTarget = null;
  input.focus();

  const title = currentMessages.find(m => m.role === "user")?.content.slice(0, 30) || "Conversation";
  upsertSession(title);
  updateTokens();
}

function stopGeneration() {
  if (abortController) abortController.abort();
  sendBtn.style.display = "flex";
  stopBtn.style.display = "none";
}

/* ============ MESSAGES UI ============ */
function addMessage(text, type, withActions = true, renderMd = true) {
  const div = document.createElement("div");
  div.className = "msg " + type;
  div.innerHTML = (type === "bot" && renderMd) ? renderMarkdown(text) : escapeHtml(text);
  if (type === "bot" && withActions && text) addActions(div, text);
  messagesEl.appendChild(div);
  messagesEl.scrollTop = messagesEl.scrollHeight;
  return div;
}

function addActions(div, text) {
  const actions = document.createElement("div");
  actions.className = "msg-actions";

  const copyBtn = document.createElement("button");
  copyBtn.textContent = "📋 Copier";
  copyBtn.onclick = () => {
    navigator.clipboard.writeText(text);
    copyBtn.textContent = "✅ Copié";
    setTimeout(() => (copyBtn.textContent = "📋 Copier"), 1200);
  };

  const speakBtn = document.createElement("button");
  speakBtn.textContent = "🔊 Écouter";
  speakBtn.onclick = () => browserSpeak(text);

  const regenBtn = document.createElement("button");
  regenBtn.textContent = "🔄 Regenerate";
  regenBtn.onclick = async () => {
    if (!lastUserMessage) return;
    div.innerHTML = "…";
    currentMessages = currentMessages.filter((m, i) =>
      !(i === currentMessages.length - 1 && m.role === "assistant")
    );
    input.value = lastUserMessage;
    send();
  };

  actions.appendChild(copyBtn);
  actions.appendChild(speakBtn);
  actions.appendChild(regenBtn);
  div.appendChild(actions);
}

/* ============ VOCAL ============ */
function browserSpeak(text) {
  const clean = text.replace(/```[\s\S]*?```/g, " bloc de code ").replace(/[*_`#>]/g, "");
  const u = new SpeechSynthesisUtterance(clean.slice(0, 2000));
  u.lang = "fr-FR";
  speechSynthesis.speak(u);
}

function toggleMic() {
  const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
  if (!SR) { alert("Reconnaissance vocale non supportée. Utilise Chrome/Edge."); return; }
  if (isRecording) { recognition.stop(); return; }

  recognition = new SR();
  recognition.lang = "fr-FR";
  recognition.continuous = false;
  recognition.interimResults = true;

  recognition.onstart = () => {
    isRecording = true;
    $("micBtn").style.color = "var(--magenta)";
    $("micBtn").style.borderColor = "var(--magenta)";
  };
  recognition.onresult = (e) => {
    const t = Array.from(e.results).map(r => r[0].transcript).join("");
    input.value = t;
    input.style.height = "auto";
    input.style.height = Math.min(input.scrollHeight, 150) + "px";
  };
  recognition.onend = () => {
    isRecording = false;
    $("micBtn").style.color = "";
    $("micBtn").style.borderColor = "";
  };
  recognition.start();
}

/* ============ IMAGE ============ */
function handleImage(e) {
  const file = e.target.files[0]; if (!file) return;
  const reader = new FileReader();
  reader.onload = (ev) => {
    currentImage = ev.target.result;
    $("previewImg").src = currentImage;
    $("preview").style.display = "block";
  };
  reader.readAsDataURL(file);
}
function clearImage() {
  currentImage = null;
  $("preview").style.display = "none";
  $("fileInput").value = "";
}

/* ============ EXPORT ============ */
function exportChat() {
  if (!currentMessages.length) return;
  const txt = currentMessages
    .map(m => `## ${m.role === "user" ? "👤 Moi" : "⚡ Vox-GPT"}\n\n${m.content}`)
    .join("\n\n---\n\n");
  const blob = new Blob([`# Vox-GPT - Conversation\n\n${txt}`], { type: "text/markdown" });
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = `voxgpt-${Date.now()}.md`;
  a.click();
}

/* ============ TOKENS ============ */
function updateTokens() {
  const chars = currentMessages.reduce((a, m) => a + (m.content?.length || 0), 0);
  $("tokenCount").textContent = `~${Math.ceil(chars / 4)} tokens`;
}

/* ============ RESET ============ */
function resetChat() {
  currentSession = newSessionId();
  currentMessages = [];
  messagesEl.innerHTML = `
    <div class="welcome">
      <h1>Bienvenue sur <span class="glow">Vox-GPT</span></h1>
      <p>Assistant IA nouvelle génération ⚡</p>
      <div class="suggestions">
        <button onclick="quickAsk('Explique-moi les trous noirs simplement')">🌌 Trous noirs</button>
        <button onclick="quickAsk('/search actualité IA 2025')">🔍 Recherche web</button>
        <button onclick="quickAsk('/py print([x**2 for x in range(10)])')">🐍 Python</button>
        <button onclick="quickAsk('12 * (34 + 56) / 2')">🧮 Calcul</button>
      </div>
    </div>`;
  renderHistory();
  updateTokens();
  input.focus();
}

function quickAsk(text) { input.value = text; send(); }

/* ============ MODALS ============ */
function openSettings() {
  $("systemPrompt").value = localStorage.getItem(userKey("system")) || "";
  $("settingsModal").style.display = "flex";
}
function saveSettings() {
  localStorage.setItem(userKey("system"), $("systemPrompt").value);
  closeModal("settingsModal");
}
function closeModal(id) { $(id).style.display = "none"; }

/* ============ THEME ============ */
$("theme").addEventListener("change", (e) => {
  document.body.setAttribute("data-theme", e.target.value);
  localStorage.setItem(userKey("theme"), e.target.value);
});

/* ============ AUTO RESIZE ============ */
input.addEventListener("input", () => {
  input.style.height = "auto";
  input.style.height = Math.min(input.scrollHeight, 150) + "px";
});

/* ============ RACCOURCIS ============ */
document.addEventListener("keydown", (e) => {
  if (e.key === "Enter" && !e.shiftKey && document.activeElement === input) {
    e.preventDefault(); send();
  }
  if (e.ctrlKey && e.key === "k") { e.preventDefault(); resetChat(); }
  if (e.ctrlKey && e.key === "e") { e.preventDefault(); exportChat(); }
  if (e.key === "Enter" && document.activeElement === $("loginPassword")) submitAuth();
  if (e.key === "Enter" && document.activeElement === $("loginEmail")) $("loginPassword").focus();
});

/* ============ INIT ============ */
(async function init() {
  pdfjsLib.GlobalWorkerOptions.workerSrc =
    "https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.worker.min.js";

  const savedUser = localStorage.getItem("vox_current_user");
  if (savedUser) {
    currentUser = { email: savedUser };
    showApp();
    const theme = localStorage.getItem(userKey("theme")) || "neon";
    document.body.setAttribute("data-theme", theme);
    $("theme").value = theme;
  } else {
    $("loginScreen").style.display = "flex";
    $("loginEmail").focus();
  }
})();
</script>

<!-- PWA -->
<link rel="manifest" href="data:application/manifest+json,%7B%22name%22%3A%22Vox-GPT%22%2C%22short_name%22%3A%22Vox%22%2C%22start_url%22%3A%22.%22%2C%22display%22%3A%22standalone%22%2C%22background_color%22%3A%22%2305050a%22%2C%22theme_color%22%3A%22%2300f0ff%22%7D">
</body>
</html>
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
