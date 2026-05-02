<!DOCTYPE html><html lang="pt-br">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
<title>ProChat v4 Profissional</title><style>
:root {
  --primary: #075e54;
  --secondary: #128c7e;
  --accent: #25d366;
  --danger: #e74c3c;
  --light: #f0f2f5;
  --bg: #efe7de;
}

* { margin: 0; padding: 0; box-sizing: border-box; }

body {
  font-family: Arial, sans-serif;
  background: #d1d7db;
  height: 100vh;
  overflow: hidden;
}

#app {
  max-width: 500px;
  margin: auto;
  height: 100vh;
  display: flex;
  flex-direction: column;
  background: var(--bg);
}

header {
  background: var(--primary);
  color: white;
  padding: 12px;
  display: flex;
  align-items: center;
  gap: 8px;
}

header .title {
  flex: 1;
}

.btn {
  border: none;
  padding: 8px 12px;
  border-radius: 18px;
  cursor: pointer;
  font-weight: bold;
}

.btn-video { background: var(--accent); color: white; }
.btn-audio { background: #3498db; color: white; }
.btn-end { background: var(--danger); color: white; }

#callArea {
  display: none;
  background: black;
  height: 180px;
}

video {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

#messages {
  flex: 1;
  overflow-y: auto;
  padding: 15px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  background: #ece5dd;
}

.msg {
  max-width: 80%;
  padding: 10px 14px;
  border-radius: 10px;
  cursor: pointer;
}

.sent {
  align-self: flex-end;
  background: #dcf8c6;
}

.received {
  align-self: flex-start;
  background: white;
}

footer {
  background: var(--light);
  padding: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
}

input[type="text"] {
  flex: 1;
  border: none;
  border-radius: 20px;
  padding: 12px;
  font-size: 15px;
}

#sendBtn, #fileBtn {
  width: 46px;
  height: 46px;
  border: none;
  border-radius: 50%;
  cursor: pointer;
  font-size: 18px;
}

#sendBtn {
  background: var(--secondary);
  color: white;
}

#fileBtn {
  background: white;
}

#menu {
  display: none;
  position: fixed;
  background: white;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.2);
  width: 200px;
  z-index: 999;
}

.item {
  padding: 14px;
  border-bottom: 1px solid #eee;
  cursor: pointer;
}

.item:last-child { border-bottom: none; }

#login {
  position: fixed;
  inset: 0;
  background: var(--primary);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2000;
}

.card {
  background: white;
  width: 90%;
  max-width: 340px;
  padding: 30px;
  border-radius: 18px;
  text-align: center;
}
</style></head>
<body><div id="login">
  <div class="card">
    <h2>ProChat</h2>
    <br>
    <input id="username" type="text" placeholder="Seu nome" style="width:100%;border:1px solid #ddd;" />
    <br><br>
    <button class="btn btn-video" style="width:100%;" onclick="entrar()">ENTRAR</button>
  </div>
</div><div id="menu">
  <div class="item" onclick="acao('share')">📤 Partilhar</div>
  <div class="item" onclick="acao('copy')">📄 Copiar</div>
  <div class="item" onclick="acao('forward')">↪️ Reencaminhar</div>
  <div class="item" onclick="acao('paste')">📋 Colar</div>
  <div class="item" onclick="acao('delete')">🗑️ Apagar</div>
</div><div id="app">
  <header>
    <div class="title">
      <strong id="nome">Usuário</strong>
    </div><button class="btn btn-video" onclick="startVideoCall()">📹</button>
<button class="btn btn-audio" onclick="startAudioCall()">📞</button>
<button class="btn btn-end" onclick="endCall()">❌</button>

  </header>  <div id="callArea">
    <video id="video" autoplay muted playsinline></video>
  </div>  <div id="messages"></div>  <footer>
    <input type="file" id="fileInput" hidden onchange="uploadFile(event)">
    <button id="fileBtn" onclick="document.getElementById('fileInput').click()">📎</button>
    <input id="text" type="text" placeholder="Digite uma mensagem" />
    <button id="sendBtn" onclick="sendMessage()">➤</button>
  </footer>
</div><script>
let user = "";
let selectedText = "";
let selectedElement = null;
let stream = null;

function entrar() {
  user = document.getElementById("username").value.trim();
  if (!user) return alert("Digite seu nome");

  document.getElementById("nome").innerText = user;
  document.getElementById("login").style.display = "none";
}

function sendMessage() {
  const input = document.getElementById("text");
  if (!input.value.trim()) return;

  createMessage(input.value, true);
  input.value = "";
}

function createMessage(text, mine = false) {
  const div = document.createElement("div");
  div.className = "msg " + (mine ? "sent" : "received");
  div.innerText = text;

  div.onclick = (e) => {
    e.stopPropagation();
    selectedText = text;
    selectedElement = div;

    const menu = document.getElementById("menu");
    menu.style.display = "block";
    menu.style.left = e.clientX + "px";
    menu.style.top = e.clientY + "px";
  };

  document.getElementById("messages").appendChild(div);
}

async function acao(tipo) {
  const input = document.getElementById("text");

  if (tipo === "copy") {
    await navigator.clipboard.writeText(selectedText);
  }

  if (tipo === "forward") {
    input.value = selectedText;
    input.focus();
  }

  if (tipo === "paste") {
    const t = await navigator.clipboard.readText();
    input.value += t;
  }

  if (tipo === "share") {
    if (navigator.share) {
      await navigator.share({ text: selectedText });
    } else {
      alert("Partilha não suportada neste dispositivo");
    }
  }

  if (tipo === "delete") {
    if (selectedElement) selectedElement.remove();
  }

  document.getElementById("menu").style.display = "none";
}

async function startVideoCall() {
  try {
    stream = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: true
    });

    document.getElementById("callArea").style.display = "block";
    document.getElementById("video").srcObject = stream;
  } catch {
    alert("Erro ao iniciar chamada de vídeo");
  }
}

async function startAudioCall() {
  try {
    stream = await navigator.mediaDevices.getUserMedia({
      audio: true,
      video: false
    });

    alert("Chamada de voz iniciada com sucesso");
  } catch {
    alert("Erro ao iniciar chamada de voz");
  }
}

function endCall() {
  if (stream) {
    stream.getTracks().forEach(track => track.stop());
  }

  document.getElementById("callArea").style.display = "none";
  document.getElementById("video").srcObject = null;
}

function uploadFile(event) {
  const file = event.target.files[0];
  if (!file) return;

  createMessage("📎 Arquivo enviado: " + file.name, true);
}

window.onclick = () => {
  document.getElementById("menu").style.display = "none";
};
</script></body>
</html>
