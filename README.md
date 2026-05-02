
<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>ProChat | D4n@s</title>
  <style>
    :root {
      --primary: #075e54;
      --secondary: #128c7e;
      --accent: #25d366;
      --bg-light: #efe7de;
    }
    * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
    body, html {
  margin: 0; height: 100%;
  font-family: 'Segoe UI', Roboto, Arial, sans-serif;
  background: #d1d7db;
}

    /* LOGIN */
    #tela-login {
      position: fixed; inset: 0;
      background: linear-gradient(135deg, var(--primary), var(--secondary));
      display: flex; align-items: center; justify-content: center;
      z-index: 2000; transition: all 0.5s ease;
    }
    .card-login {
      background: white; padding: 35px;
      border-radius: 24px; width: 90%; max-width: 360px;
      text-align: center; box-shadow: 0 10px 25px rgba(0,0,0,0.2);
      animation: slideUp 0.6s ease;
    }
    @keyframes slideUp {
      from { transform: translateY(30px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
    #preview-foto {
      width: 100px; height: 100px; border-radius: 50%;
      cursor: pointer; border: 4px solid var(--accent);
      box-shadow: 0 0 10px 3px var(--accent);
      object-fit: cover; transition: transform 0.3s;
    }
    #preview-foto:active { transform: scale(0.9); }
    .card-login h3 { color: var(--primary); margin: 15px 0; }
    #input-nome {
      width: 100%; padding: 12px; margin: 10px 0;
      border: 1px solid #ddd; border-radius: 8px;
      font-size: 15px;
    }
    #select-estado {
      width: 100%; padding: 12px; margin-bottom: 15px;
      border-radius: 8px; border: 1px solid #ddd;
      font-size: 15px;
    }
    #btn-entrar {
      width: 100%; padding: 14px;
      background: var(--accent); border: none;
      border-radius: 8px; color: white;
      font-weight: bold; font-size: 16px; cursor: pointer;
    }

    /* APP */
    #app-container {
  display: flex; flex-direction: column;
  height: 100dvh;
  max-width: 500px;
  margin: 0 auto;
  background: var(--bg-light);
  overflow: hidden;
}
    header {
      background: rgba(7,94,84,0.97);
      backdrop-filter: blur(10px);
      color: white; padding: 12px 18px;
      display: flex; align-items: center; gap: 12px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    }
    .avatar-wrapper { position: relative; }
    #user-avatar {
      width: 42px; height: 42px;
      border-radius: 50%; object-fit: cover;
    }
    .status-dot {
      width: 12px; height: 12px;
      border: 2px solid white; border-radius: 50%;
      position: absolute; bottom: 2px; right: 2px;
      background: var(--accent);
    }

    /* MENSAGENS */
    #mensagens {
      flex: 1; overflow-y: auto; padding: 15px;
      background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png');
      display: flex; flex-direction: column; gap: 6px;
      scroll-behavior: smooth;
    }
    .msg {
      max-width: 85%; padding: 8px 12px;
      border-radius: 12px; font-size: 15px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.15);
      animation: popIn 0.3s ease-out;
      position: relative; word-wrap: break-word;
    }
    @keyframes popIn {
      from { transform: scale(0.8); opacity: 0; }
      to { transform: scale(1); opacity: 1; }
    }
    .sent {
      align-self: flex-end; background: #e7ffdb;
      border-top-right-radius: 2px;
    }
    .received {
      align-self: flex-start; background: white;
      border-top-left-radius: 2px;
    }
    .msg-info {
      font-size: 10px; color: #888;
      display: flex; justify-content: flex-end;
      align-items: center; gap: 3px; margin-top: 4px;
    }
    .tick { font-size: 13px; color: #888; }
    .tick.entregue { color: #4fc3f7; }
    .tick.lido { color: var(--accent); }

    /* INDICADOR A ESCREVER */
    #a-escrever {
      padding: 5px 20px; font-size: 13px;
      color: var(--secondary); font-style: italic;
      min-height: 24px;
    }

    /* VÍDEO */
    #area-video {
      display: none; background: #000;
      height: 180px; padding: 10px;
      justify-content: center;
    }
    #v-local {
      height: 100%; border-radius: 12px;
      border: 2px solid var(--accent);
    }

    /* FOOTER */
    footer {
      background: #f0f2f5; padding: 10px 15px;
      display: flex; align-items: center; gap: 8px;
    }
    .input-wrapper {
      flex: 1; background: white;
      border-radius: 25px; padding: 5px 15px;
      display: flex; align-items: center;
    }
    #campo-texto {
      flex: 1; border: none; padding: 10px 0;
      outline: none; font-size: 16px; background: transparent;
    }
    .btn-action {
      width: 45px; height: 45px; border-radius: 50%;
      border: none; background: var(--secondary);
      color: white; cursor: pointer;
      display: flex; align-items: center; justify-content: center;
      font-size: 18px; transition: 0.2s;
    }
    .btn-action:active { transform: scale(0.85); }

    .signature-watermark {
      position: absolute; bottom: 70px; left: 50%;
      transform: translateX(-50%);
      font-size: 11px; color: rgba(0,0,0,0.08);
      font-weight: bold; pointer-events: none;
      letter-spacing: 3px;
    }
  .menu-item {
  padding: 14px 20px;
  font-size: 15px;
  cursor: pointer;
  border-bottom: 1px solid #f0f0f0;
  transition: background 0.2s;
}
.menu-item:hover { background: #f5f5f5; }
.menu-item:last-child { border-bottom: none; }
#overlay-menu {
  display: none;
  position: fixed;
  inset: 0;
  z-index: 9998;
}
  </style>
</head>
<body>

  <!-- LOGIN -->
  <div id="tela-login">
    <div class="card-login">
      <div class="avatar-wrapper" style="display:inline-block">
        <img id="preview-foto" src="https://ui-avatars.com/api/?name=?&background=075e54&color=fff&size=150" title="Clique para adicionar foto">
      </div>
      <input type="file" id="input-foto" hidden accept="image/*">
      <h3>ProChat</h3>
      <input type="text" id="input-nome" placeholder="O teu nome..." maxlength="15">
      <select id="select-estado">
        <option value="Online">🟢 Online</option>
        <option value="Ocupado">🟡 Ocupado</option>
        <option value="Ausente">🔴 Ausente</option>
      </select>
      <button id="btn-entrar">ENTRAR NO CHAT</button>
    </div>
  </div>

  <!-- APP -->
  <div id="app-container">
    <header>
      <div class="avatar-wrapper">
        <img src="" id="user-avatar">
        <div id="status-indicador" class="status-dot"></div>
      </div>
      <div style="flex:1">
        <div id="display-nome" style="font-weight:600; font-size:17px">Nome</div>
        <div id="display-estado" style="font-size:12px; opacity:0.9">Online</div>
      </div>
      <button class="btn-action" style="background:none" onclick="ativarVideo()">🎥</button>
      <button class="btn-action" style="background:none" onclick="ativarVoz()">📞</button>
    </header>

    <div id="area-video">
      <video id="v-local" autoplay muted playsinline></video>
    </div>

    <div id="mensagens"></div>
    <div id="a-escrever"></div>
    <div class="signature-watermark">D4n@s</div>

    <footer>
      <button class="btn-action" style="background:#e3e6eb; color:#54656f; font-size:24px; font-weight:bold">+</button>
      <div class="input-wrapper">
        <input type="text" id="campo-texto" placeholder="Mensagem">
      </div>
      <button class="btn-action" id="btn-enviar">➤</button>
    </footer>
  </div>
  <!-- MENU DE OPÇÕES -->
<div id="menu-opcoes" style="
  display:none;
  position:fixed;
  background:white;
  border-radius:12px;
  box-shadow: 0 4px 20px rgba(0,0,0,0.2);
  z-index:9999;
  overflow:hidden;
  min-width:180px;
">
  <div class="menu-item" id="opcao-copiar">📋 Copiar</div>
  <div class="menu-item" id="opcao-reencaminhar">↪️ Reencaminhar</div>
  <div class="menu-item" id="opcao-apagar" style="color:red">🗑️ Apagar</div>
</div>
  <div id="overlay-menu"></div>

  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // Firebase
    firebase.initializeApp({
      databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com/"
    });
    const db = firebase.database();

    const myId = Math.random().toString(36).substring(7);
    let myName = "";
    let myPhoto = "https://ui-avatars.com/api/?name=?&background=075e54&color=fff&size=150";

    // Foto de perfil
    document.getElementById("preview-foto").onclick = () =>
      document.getElementById("input-foto").click();

    document.getElementById("input-foto").onchange = (e) => {
      const reader = new FileReader();
      reader.onload = (ev) => {
        document.getElementById("preview-foto").src = ev.target.result;
        myPhoto = ev.target.result;
      };
      reader.readAsDataURL(e.target.files[0]);
    };

    // Cor do anel conforme estado
    document.getElementById("select-estado").onchange = function() {
      const cores = { Online: "#25d366", Ocupado: "#f0c000", Ausente: "#e74c3c" };
      const foto = document.getElementById("preview-foto");
      foto.style.borderColor = cores[this.value];
      foto.style.boxShadow = `0 0 10px 3px ${cores[this.value]}`;
    };

    // Login
    document.getElementById("btn-entrar").onclick = () => {
      myName = document.getElementById("input-nome").value.trim();
      if (!myName) return alert("Escreve o teu nome!");

      const estado = document.getElementById("select-estado").value;
      const cores = { Online: "#25d366", Ocupado: "#f0c000", Ausente: "#e74c3c" };

      document.getElementById("display-nome").innerText = myName;
      document.getElementById("display-estado").innerText = estado;
      document.getElementById("status-indicador").style.background = cores[estado];
      document.getElementById("user-avatar").src = myPhoto;

      const tela = document.getElementById("tela-login");
      tela.style.opacity = "0";
      tela.style.transform = "scale(1.1)";
      setTimeout(() => tela.style.display = "none", 500);
    };

    // Enviar mensagem
    function enviar() {
      const input = document.getElementById("campo-texto");
      const texto = input.value.trim();
      if (!texto) return;

      db.ref("mensagens").push({
        senderId: myId,
        nome: myName,
        texto: texto,
        timestamp: Date.now(),
        entregue: true,
        lido: false
      });

      // Parar indicador de escrita
      db.ref("digitando/" + myId).remove();
      input.value = "";
    }

    document.getElementById("btn-enviar").onclick = enviar;
    document.getElementById("campo-texto").onkeypress = (e) => {
      if (e.key === "Enter") { enviar(); return; }
    };

    // Indicador "a escrever..."
    document.getElementById("campo-texto").oninput = () => {
      db.ref("digitando/" + myId).set({ nome: myName });
      clearTimeout(typingTimer);
      typingTimer = setTimeout(() => {
        db.ref("digitando/" + myId).remove();
      }, 2000);
    };

    // Escutar quem está a escrever
    db.ref("digitando").on("value", (snap) => {
      const dados = snap.val();
      const indicador = document.getElementById("a-escrever");
      if (!dados) { indicador.innerText = ""; return; }

      const outros = Object.values(dados)
        .filter(d => d.nome !== myName)
        .map(d => d.nome);

      if (outros.length === 0) {
        indicador.innerText = "";
      } else if (outros.length === 1) {
        indicador.innerText = outros[0] + " está a escrever...";
      } else {
        indicador.innerText = "Vários utilizadores estão a escrever...";
      }
    });

    // Receber mensagens
    db.ref("mensagens").limitToLast(50).on("child_added", (snap) => {
      const data = snap.val();
      const area = document.getElementById("mensagens");
      const isMe = data.senderId === myId;

      // Marcar como lido se for mensagem recebida
      if (!isMe) {
        db.ref("mensagens/" + snap.key).update({ lido: true });
      }

      const div = document.createElement("div");
      div.className = "msg " + (isMe ? "sent" : "received");
      div.id = "msg-" + snap.key;

      // Ticks de entrega
      let ticks = "";
      if (isMe) {
        if (data.lido) {
          ticks = `<span class="tick lido">✓✓</span>`;
        } else if (data.entregue) {
          ticks = `<span class="tick entregue">✓✓</span>`;
        } else {
          ticks = `<span class="tick">✓</span>`;
        }
      }

      div.innerHTML = `
        <small style="display:block; font-size:10px; font-weight:bold; color:var(--secondary); margin-bottom:2px">
          ${isMe ? "Você" : data.nome}
        </small>
        ${data.texto}
        <span class="msg-info">
          ${new Date(data.timestamp).toLocaleTimeString([], {hour:"2-digit", minute:"2-digit"})}
          ${ticks}
        </span>
      `;

      // Apagar ao pressionar e segurar
      let pressTimer;
      const iniciar = (e) => {
  pressTimer = setTimeout(() => {
    abrirMenu(e, div, snap, isMe);
  }, 600);
};
   
      const cancelar = () => clearTimeout(pressTimer);

      div.addEventListener("mousedown", iniciar);
      div.addEventListener("mouseup", cancelar);
      div.addEventListener("touchstart", iniciar);
      div.addEventListener("touchend", cancelar);
      div.addEventListener("touchmove", cancelar);

      area.appendChild(div);
      area.scrollTop = area.scrollHeight;
    });

    // Actualizar ticks quando mensagem é lida
    db.ref("mensagens").on("child_changed", (snap) => {
      const data = snap.val();
      const msgDiv = document.getElementById("msg-" + snap.key);
      if (!msgDiv) return;
      if (data.senderId !== myId) return;

      const tickEl = msgDiv.querySelector(".tick");
      if (!tickEl) return;

      if (data.lido) {
        tickEl.className = "tick lido";
        tickEl.innerText = "✓✓";
      } else if (data.entregue) {
        tickEl.className = "tick entregue";
        tickEl.innerText = "✓✓";
      }
    });

    // Remover mensagem apagada
  // Menu de opções
let msgSelecionada = null;
let textoSelecionado = "";
const menu = document.getElementById("menu-opcoes");
const overlay = document.getElementById("overlay-menu");

function abrirMenu(e, div, snap, isMe) {
  msgSelecionada = { snap, div, isMe };
  textoSelecionado = snap.val().texto;

  // Mostrar ou esconder opção apagar
  document.getElementById("opcao-apagar").style.display = isMe ? "block" : "none";

  // Posicionar menu
  const x = Math.min(e.clientX || e.touches[0].clientX, window.innerWidth - 200);
  const y = Math.min(e.clientY || e.touches[0].clientY, window.innerHeight - 150);
  menu.style.left = x + "px";
  menu.style.top = y + "px";
  menu.style.display = "block";
  overlay.style.display = "block";
}

function fecharMenu() {
  menu.style.display = "none";
  overlay.style.display = "none";
  msgSelecionada = null;
}

overlay.onclick = fecharMenu;

// Copiar
document.getElementById("opcao-copiar").onclick = () => {
  navigator.clipboard.writeText(textoSelecionado)
    .then(() => alert("✅ Mensagem copiada!"))
    .catch(() => alert("Erro ao copiar"));
  fecharMenu();
};

// Reencaminhar
document.getElementById("opcao-reencaminhar").onclick = () => {
  document.getElementById("campo-texto").value = textoSelecionado;
  document.getElementById("campo-texto").focus();
  fecharMenu();
};

// Apagar
document.getElementById("opcao-apagar").onclick = () => {
  if (confirm("Apagar esta mensagem para todos?")) {
    db.ref("mensagens/" + msgSelecionada.snap.key).remove();
  }
  fecharMenu();
};
    db.ref("mensagens").on("child_removed", (snap) => {
      const el = document.getElementById("msg-" + snap.key);
      if (el) el.remove();
    });

    // Vídeo
    function ativarVideo() {
      const area = document.getElementById("area-video");
      area.style.display = area.style.display === "none" ? "flex" : "none";
      if (area.style.display === "flex") {
        navigator.mediaDevices.getUserMedia({ video: true, audio: true })
          .then(s => document.getElementById("v-local").srcObject = s)
          .catch(err => alert("Erro: " + err.message));
      }
    }

    // Voz
    function ativarVoz() {
      navigator.mediaDevices.getUserMedia({ video: false, audio: true })
        .then(() => alert("🎙️ Microfone activado!"))
        .catch(err => alert("Erro: " + err.message));
    }
  </script>
</body>
</html>
