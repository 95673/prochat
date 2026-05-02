<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>ProChat | D4n@s</title>
  <style>
    :root {
      --primary: #075e54;
      --secondary: #128c7e;
      --accent: #25d366;
      --bg-light: #efe7de;
      /* Variável dinâmica para altura mobile */
      --vh: 1vh;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    html, body {
      height: 100%;
      height: calc(var(--vh, 1vh) * 100);
      font-family: 'Segoe UI', Roboto, Arial, sans-serif;
      background: #d1d7db;
      overflow: hidden;
      display: flex;
      flex-direction: column;
    }

    /* TELA DE LOGIN */
    #tela-login {
      position: fixed; 
      inset: 0;
      background: linear-gradient(135deg, var(--primary), var(--secondary));
      display: flex; 
      align-items: center; 
      justify-content: center;
      z-index: 2000; 
      transition: all 0.5s ease;
    }
    .card-login {
      background: white; 
      padding: 30px;
      border-radius: 24px; 
      width: 90%; 
      max-width: 360px;
      text-align: center; 
      box-shadow: 0 10px 25px rgba(0,0,0,0.2);
    }

    #preview-foto {
      width: 100px; height: 100px; border-radius: 50%;
      cursor: pointer; border: 4px solid var(--accent);
      object-fit: cover; transition: transform 0.3s;
    }

    #input-nome, #input-sala, #select-estado {
      width: 100%; padding: 12px; margin: 8px 0;
      border: 1px solid #ddd; border-radius: 8px;
      font-size: 15px; outline: none;
    }

    #btn-entrar {
      width: 100%; padding: 14px;
      background: var(--accent); border: none;
      border-radius: 8px; color: white;
      font-weight: bold; font-size: 16px; cursor: pointer;
      margin-top: 10px;
    }

    /* CONTAINER PRINCIPAL DO APP */
    #app-container {
      display: flex;
      flex-direction: column;
      width: 100%;
      max-width: 500px;
      height: 100%;
      margin: 0 auto;
      background: var(--bg-light);
      position: relative;
    }

    header {
      background: var(--primary);
      color: white; padding: 10px 15px;
      display: flex; align-items: center; gap: 12px;
      flex-shrink: 0;
      z-index: 100;
    }

    #user-avatar { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; }
    .status-dot {
      width: 12px; height: 12px; border: 2px solid white;
      border-radius: 50%; position: absolute; bottom: 2px; right: 2px;
      background: var(--accent);
    }

    /* ÁREA DE MENSAGENS */
    #mensagens {
      flex: 1;
      overflow-y: auto;
      padding: 15px;
      background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png');
      display: flex; flex-direction: column; gap: 8px;
      -webkit-overflow-scrolling: touch;
    }

    .msg {
      max-width: 85%; padding: 8px 12px;
      border-radius: 12px; font-size: 15px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.1);
      word-wrap: break-word;
      position: relative;
    }
    .sent { align-self: flex-end; background: #e7ffdb; border-top-right-radius: 2px; }
    .received { align-self: flex-start; background: white; border-top-left-radius: 2px; }

    .msg-info { font-size: 10px; color: #888; display: flex; justify-content: flex-end; gap: 3px; margin-top: 4px; }
    .tick.lido { color: #4fc3f7; }

    #a-escrever { padding: 5px 20px; font-size: 12px; color: var(--secondary); min-height: 20px; }

    /* RODAPÉ FIXO */
    footer {
      background: #f0f2f5;
      padding: 10px;
      display: flex;
      align-items: center;
      gap: 8px;
      flex-shrink: 0;
      padding-bottom: env(safe-area-inset-bottom, 10px);
    }

    .input-wrapper { flex: 1; background: white; border-radius: 20px; padding: 5px 15px; }
    #campo-texto { width: 100%; border: none; padding: 8px 0; outline: none; font-size: 16px; }

    .btn-action {
      width: 40px; height: 40px; border-radius: 50%; border: none;
      background: var(--secondary); color: white; cursor: pointer;
      display: flex; align-items: center; justify-content: center; flex-shrink: 0;
    }

    /* MENU DE CONTEXTO */
    #menu-opcoes {
      display: none; position: absolute; background: white;
      border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.2);
      z-index: 10000; min-width: 140px;
    }
    .menu-item { padding: 12px; cursor: pointer; border-bottom: 1px solid #eee; }
    #overlay-menu { display: none; position: fixed; inset: 0; z-index: 9999; }
    
    .signature-watermark { text-align: center; font-size: 10px; color: #bbb; padding: 2px; }
  </style>
</head>
<body>

  <!-- TELA DE LOGIN -->
  <div id="tela-login">
    <div class="card-login">
      <div class="avatar-wrapper" style="position:relative; display:inline-block">
        <img id="preview-foto" src="https://ui-avatars.com/api/?name=?&background=075e54&color=fff&size=150">
        <input type="file" id="input-foto" hidden accept="image/*">
      </div>
      <h3>ProChat</h3>
      <input type="text" id="input-nome" placeholder="O teu nome..." maxlength="15">
      <input type="text" id="input-sala" placeholder="Nome da sala (ex: sala1)..." maxlength="20">
      <select id="select-estado">
        <option value="Online">🟢 Online</option>
        <option value="Ocupado">🟡 Ocupado</option>
        <option value="Ausente">🔴 Ausente</option>
      </select>
      <button id="btn-entrar">ENTRAR NO CHAT</button>
    </div>
  </div>

  <!-- APP CONTAINER -->
  <div id="app-container">
    <header>
      <div style="position:relative">
        <img src="" id="user-avatar">
        <div id="status-indicador" class="status-dot"></div>
      </div>
      <div style="flex:1">
        <div id="display-nome" style="font-weight:600">Nome</div>
        <div id="display-estado" style="font-size:12px">Online</div>
      </div>
      <button class="btn-action" style="background:none" onclick="ativarVideo()">🎥</button>
    </header>

    <div id="area-video" style="display:none; background:black; height:150px; flex-shrink:0;">
      <video id="v-local" autoplay muted playsinline style="height:100%; margin:0 auto;"></video>
    </div>

    <div id="mensagens"></div>
    <div id="a-escrever"></div>
    <div class="signature-watermark">D4n@s</div>

    <footer>
      <button class="btn-action" style="background:#ddd; color:#333">+</button>
      <div class="input-wrapper">
        <input type="text" id="campo-texto" placeholder="Mensagem">
      </div>
      <button class="btn-action" id="btn-enviar">➤</button>
    </footer>
  </div>

  <!-- MENU -->
  <div id="menu-opcoes">
    <div class="menu-item" id="opcao-copiar">📋 Copiar</div>
    <div class="menu-item" id="opcao-apagar" style="color:red">🗑️ Apagar</div>
  </div>
  <div id="overlay-menu"></div>

  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // CORREÇÃO DE ALTURA PARA MOBILE
    function updateHeight() {
      let vh = window.innerHeight * 0.01;
      document.documentElement.style.setProperty('--vh', `${vh}px`);
    }
    window.addEventListener('resize', updateHeight);
    window.addEventListener('load', updateHeight);

    // FIREBASE
    firebase.initializeApp({ databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com/" });
    const db = firebase.database();

    const myId = Math.random().toString(36).substring(7);
    let myName = "";
    let myRoom = "geral";
    let myPhoto = "https://ui-avatars.com/api/?name=?&background=075e54&color=fff";

    // FOTO PREVIEW
    document.getElementById("preview-foto").onclick = () => document.getElementById("input-foto").click();
    document.getElementById("input-foto").onchange = (e) => {
      const reader = new FileReader();
      reader.onload = (ev) => {
        document.getElementById("preview-foto").src = ev.target.result;
        myPhoto = ev.target.result;
      };
      reader.readAsDataURL(e.target.files[0]);
    };

    // LOGIN
    document.getElementById("btn-entrar").onclick = () => {
      myName = document.getElementById("input-nome").value.trim();
      myRoom = document.getElementById("input-sala").value.trim() || "geral";
      if (!myName) return alert("Digite seu nome!");

      document.getElementById("display-nome").innerText = myName;
      document.getElementById("user-avatar").src = myPhoto;
      
      const tela = document.getElementById("tela-login");
      tela.style.opacity = "0";
      setTimeout(() => {
          tela.style.display = "none";
          ouvirMensagens();
      }, 500);
    };

    // ENVIAR MENSAGEM
    function enviar() {
      const input = document.getElementById("campo-texto");
      const texto = input.value.trim();
      if (!texto) return;

      db.ref("salas/" + myRoom + "/mensagens").push({
        senderId: myId, nome: myName, texto: texto, timestamp: Date.now()
      });
      input.value = "";
      db.ref("digitando/" + myRoom + "/" + myId).remove();
    }

    document.getElementById("btn-enviar").onclick = enviar;
    document.getElementById("campo-texto").onkeypress = (e) => { if(e.key === "Enter") enviar(); };

    // DIGITANDO
    let typingTimer;
    document.getElementById("campo-texto").oninput = () => {
      db.ref("digitando/" + myRoom + "/" + myId).set({ nome: myName });
      clearTimeout(typingTimer);
      typingTimer = setTimeout(() => db.ref("digitando/" + myRoom + "/" + myId).remove(), 2000);
    };

    db.ref("digitando/" + myRoom).on("value", (snap) => {
      const dados = snap.val();
      const indicador = document.getElementById("a-escrever");
      if (!dados) { indicador.innerText = ""; return; }
      const outros = Object.values(dados).filter(d => d.nome !== myName).map(d => d.nome);
      indicador.innerText = outros.length > 0 ? outros.join(", ") + " está escrevendo..." : "";
    });

    // LISTAR MENSAGENS
    function ouvirMensagens() {
      db.ref("salas/" + myRoom + "/mensagens").limitToLast(50).on("child_added", (snap) => {
        const data = snap.val();
        const area = document.getElementById("mensagens");
        const isMe = data.senderId === myId;

        const div = document.createElement("div");
        div.className = "msg " + (isMe ? "sent" : "received");
        div.innerHTML = `
          <small style="display:block; font-weight:bold; font-size:10px; color:#128c7e">${isMe ? "Você" : data.nome}</small>
          ${data.texto}
          <div class="msg-info">${new Date(data.timestamp).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</div>
        `;
        area.appendChild(div);
        area.scrollTop = area.scrollHeight;
      });
    }

    function ativarVideo() {
      const v = document.getElementById("area-video");
      v.style.display = v.style.display === "none" ? "block" : "none";
      if(v.style.display === "block") {
        navigator.mediaDevices.getUserMedia({video: true}).then(s => {
          document.getElementById("v-local").srcObject = s;
        });
      }
    }
    const campoTexto = document.getElementById("campo-texto");

campoTexto.addEventListener("focus", () => {
  // Pequeno delay para esperar o teclado subir
  setTimeout(() => {
    // Faz o campo de texto ficar visível na tela
    campoTexto.scrollIntoView({ behavior: "smooth", block: "center" });
    
    // Garante que o chat role para a última mensagem
    const areaMsgs = document.getElementById("mensagens");
    areaMsgs.scrollTop = areaMsgs.scrollHeight;
  }, 300);
});
  </script>
</body>
</html>
