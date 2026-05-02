<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>ProChat v3 | D4n@s</title>
  <style>
    :root {
      --primary: #075e54;
      --secondary: #128c7e;
      --accent: #25d366;
      --bg-light: #efe7de;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    html, body {
      position: fixed; overflow: hidden;
      width: 100%; height: 100%;
      background-color: #d1d7db;
      font-family: 'Segoe UI', Roboto, sans-serif;
    }

    #app-container {
      display: flex; flex-direction: column;
      width: 100%; max-width: 500px;
      margin: 0 auto; background: var(--bg-light);
      height: 100%; position: relative;
    }

    /* HEADER */
    header {
      background: var(--primary); color: white;
      padding: 10px 15px; display: flex;
      align-items: center; gap: 12px; flex-shrink: 0;
    }

    .btn-call {
      background: #25d366; border: none; color: white;
      padding: 8px 12px; border-radius: 20px; cursor: pointer;
      font-weight: bold; font-size: 12px;
    }

    /* ÁREA DE MENSAGENS */
    #mensagens {
      flex: 1; overflow-y: auto; padding: 15px;
      background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png');
      display: flex; flex-direction: column; gap: 8px;
    }

    .msg {
      max-width: 85%; padding: 8px 12px; border-radius: 8px;
      font-size: 15px; box-shadow: 0 1px 1px rgba(0,0,0,0.1);
      position: relative; cursor: pointer;
      animation: fadeIn 0.3s ease;
    }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

    .sent { align-self: flex-end; background: #e7ffdb; }
    .received { align-self: flex-start; background: white; }

    /* MENU DE OPÇÕES PERSONALIZADO */
    #context-menu {
      display: none; position: fixed; background: white;
      box-shadow: 0 4px 15px rgba(0,0,0,0.2); border-radius: 12px;
      z-index: 3000; width: 180px; overflow: hidden;
    }
    .menu-item {
      padding: 14px; cursor: pointer; border-bottom: 1px solid #f0f0f0;
      font-size: 14px; color: #333; display: flex; align-items: center; gap: 10px;
    }
    .menu-item:active { background: #ececec; }
    .menu-item:last-child { border-bottom: none; }

    /* FOOTER */
    footer {
      background: #f0f2f5; padding: 10px;
      display: flex; align-items: center; gap: 8px; flex-shrink: 0;
    }
    .input-wrapper { flex: 1; background: white; border-radius: 20px; padding: 5px 15px; display: flex; }
    #campo-texto { width: 100%; border: none; padding: 10px 0; outline: none; font-size: 16px; }

    /* LOGIN */
    #tela-login {
      position: fixed; inset: 0; background: var(--primary);
      z-index: 4000; display: flex; align-items: center; justify-content: center;
    }
    .card-login { background: white; padding: 30px; border-radius: 20px; width: 90%; max-width: 350px; text-align: center; }
  </style>
</head>
<body>

  <!-- LOGIN -->
  <div id="tela-login">
    <div class="card-login">
      <h3>ProChat</h3>
      <input type="text" id="input-usuario" placeholder="Nome de Usuário" style="width:100%; padding:12px; margin:15px 0; border:1px solid #ddd; border-radius:8px;">
      <button id="btn-entrar" style="width:100%; padding:12px; background:var(--accent); color:white; border:none; border-radius:8px; font-weight:bold;">ENTRAR</button>
    </div>
  </div>

  <!-- MENU DE OPÇÕES -->
  <div id="context-menu">
    <div class="menu-item" onclick="opMsg('copy')">📄 Copiar</div>
    <div class="menu-item" onclick="opMsg('forward')">↪️ Reencaminhar</div>
    <div class="menu-item" onclick="opMsg('paste')">📋 Colar</div>
    <div class="menu-item" onclick="opMsg('select_all')">🔘 Selecionar Tudo</div>
    <div id="menu-delete" class="menu-item" style="color:red;" onclick="opMsg('delete')">🗑️ Apagar</div>
  </div>

  <div id="app-container">
    <header>
      <div style="flex:1"><strong id="display-nome">Usuário</strong></div>
      <button id="btn-chamar" class="btn-call" onclick="toggleCall(true)">📞 Chamada</button>
      <button id="btn-desligar" class="btn-call" style="background:#e74c3c; display:none;" onclick="toggleCall(false)">❌ Desligar</button>
    </header>

    <div id="area-video" style="display:none; background:black; height:180px;">
      <video id="localVideo" autoplay muted playsinline style="width:100%; height:100%; object-fit:cover;"></video>
    </div>

    <div id="mensagens"></div>

    <footer>
      <div class="input-wrapper">
        <input type="text" id="campo-texto" placeholder="Mensagem" autocomplete="off">
      </div>
      <button id="btn-enviar" style="background:var(--secondary); border:none; color:white; width:45px; height:45px; border-radius:50%; font-size:20px;">➤</button>
    </footer>
  </div>

  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // FIREBASE
    const firebaseConfig = { databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    let myUser = "";
    let msgIdSelecionada = null;
    let textoSelecionado = "";

    // ENTRAR
    document.getElementById("btn-entrar").onclick = () => {
      myUser = document.getElementById("input-usuario").value.trim().toLowerCase();
      if (!myUser) return alert("Digite um nome!");
      document.getElementById("display-nome").innerText = myUser;
      document.getElementById("tela-login").style.display = "none";
      iniciarEscuta();
    };

    // ESCUTAR MENSAGENS E REMOÇÕES
    function iniciarEscuta() {
      const area = document.getElementById("mensagens");

      // Adicionar mensagem
      db.ref("mensagens").on("child_added", (snap) => {
        const m = snap.val();
        const div = document.createElement("div");
        div.className = `msg ${m.usuario === myUser ? 'sent' : 'received'}`;
        div.id = "msg-" + snap.key;
        div.innerText = m.texto;
        
        // Clique para menu (Android/PC)
        div.onclick = (e) => mostrarMenu(e, snap.key, m.texto, m.usuario === myUser);
        
        area.appendChild(div);
        area.scrollTop = area.scrollHeight;
      });

      // Remover mensagem em tempo real sem sair do chat
      db.ref("mensagens").on("child_removed", (snap) => {
        const elemento = document.getElementById("msg-" + snap.key);
        if (elemento) {
          elemento.style.opacity = "0";
          setTimeout(() => elemento.remove(), 300);
        }
      });
    }

    // ENVIAR
    function enviar() {
      const campo = document.getElementById("campo-texto");
      if (!campo.value.trim()) return;
      db.ref("mensagens").push({ usuario: myUser, texto: campo.value.trim(), timestamp: Date.now() });
      campo.value = "";
      campo.focus();
    }
    document.getElementById("btn-enviar").onclick = enviar;
    document.getElementById("campo-texto").onkeypress = (e) => { if(e.key === 'Enter') enviar(); };

    // MENU DE CONTEXTO
    function mostrarMenu(e, id, texto, souDono) {
      e.preventDefault();
      msgIdSelecionada = id;
      textoSelecionado = texto;
      const menu = document.getElementById("context-menu");
      
      menu.style.display = "block";
      menu.style.top = Math.min(e.clientY, window.innerHeight - 250) + "px";
      menu.style.left = Math.min(e.clientX, window.innerWidth - 200) + "px";
      
      document.getElementById("menu-delete").style.display = souDono ? "flex" : "none";

      setTimeout(() => {
        window.onclick = () => { menu.style.display = "none"; window.onclick = null; };
      }, 100);
    }

    async function opMsg(tipo) {
      const campo = document.getElementById("campo-texto");
      
      if (tipo === 'copy') {
        await navigator.clipboard.writeText(textoSelecionado);
      } 
      else if (tipo === 'forward') {
        campo.value = textoSelecionado;
        campo.focus();
      } 
      else if (tipo === 'paste') {
        const t = await navigator.clipboard.readText();
        campo.value += t;
        campo.focus();
      } 
      else if (tipo === 'select_all') {
        campo.select();
        campo.focus();
      }
      else if (tipo === 'delete') {
        if(confirm("Deseja apagar esta mensagem para todos?")) {
          db.ref("mensagens/" + msgIdSelecionada).remove();
        }
      }
      document.getElementById("context-menu").style.display = "none";
    }

    // CHAMADAS
    function toggleCall(show) {
      const area = document.getElementById("area-video");
      const video = document.getElementById("localVideo");
      
      document.getElementById("btn-chamar").style.display = show ? "none" : "block";
      document.getElementById("btn-desligar").style.display = show ? "block" : "none";
      area.style.display = show ? "block" : "none";

      if (show) {
        navigator.mediaDevices.getUserMedia({ video: true, audio: true })
          .then(s => video.srcObject = s)
          .catch(e => alert("Erro na câmera"));
      } else {
        if (video.srcObject) video.srcObject.getTracks().forEach(t => t.stop());
      }
    }

    // AJUSTE MOBILE ANDROID (RODAPÉ)
    window.visualViewport.addEventListener('resize', () => {
      document.getElementById("app-container").style.height = window.visualViewport.height + "px";
      window.scrollTo(0,0);
    });
  </script>
</body>
</html>




<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>ProChat Final | D4n@s</title>
  <style>
    :root {
      --primary: #075e54;
      --secondary: #128c7e;
      --accent: #25d366;
      --bg-light: #efe7de;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; }

    /* O segredo para Android: Body fixo e sem scroll */
    html, body {
      position: fixed;
      overflow: hidden;
      width: 100%;
      height: 100%;
      background-color: #d1d7db;
      font-family: 'Segoe UI', Roboto, sans-serif;
    }

    /* Container que será redimensionado via JS */
    #app-container {
      display: flex;
      flex-direction: column;
      width: 100%;
      max-width: 500px;
      margin: 0 auto;
      background: var(--bg-light);
      height: 100%; /* Será controlado pelo JS */
      position: relative;
    }

    header {
      background: var(--primary);
      color: white;
      padding: 10px 15px;
      display: flex;
      align-items: center;
      gap: 12px;
      flex-shrink: 0;
    }

    #mensagens {
      flex: 1;
      overflow-y: auto;
      padding: 15px;
      background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png');
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .msg {
      max-width: 85%; padding: 8px 12px; border-radius: 8px;
      font-size: 15px; box-shadow: 0 1px 1px rgba(0,0,0,0.1);
    }
    .sent { align-self: flex-end; background: #e7ffdb; }
    .received { align-self: flex-start; background: white; }

    footer {
      background: #f0f2f5;
      padding: 10px;
      display: flex;
      align-items: center;
      gap: 8px;
      flex-shrink: 0;
      /* Garante que o footer fique acima de qualquer coisa */
      position: relative;
      z-index: 999;
    }

    .input-wrapper {
      flex: 1;
      background: white;
      border-radius: 20px;
      padding: 5px 15px;
    }

    #campo-texto {
      width: 100%; border: none; padding: 10px 0;
      outline: none; font-size: 16px;
    }

    .btn-action {
      width: 42px; height: 42px; border-radius: 50%;
      border: none; background: var(--secondary);
      color: white; cursor: pointer;
    }

    #tela-login {
      position: fixed; inset: 0;
      background: var(--primary);
      z-index: 2000;
      display: flex; align-items: center; justify-content: center;
    }
  </style>
</head>
<body>

  <div id="tela-login">
    <div style="background:white; padding:30px; border-radius:20px; width:90%; max-width:350px; text-align:center;">
      <h3>ProChat</h3>
      <input type="text" id="input-nome" placeholder="Seu nome..." style="width:100%; padding:12px; margin:15px 0; border:1px solid #ddd; border-radius:8px;">
      <button id="btn-entrar" style="width:100%; padding:12px; background:var(--accent); color:white; border:none; border-radius:8px; font-weight:bold;">ENTRAR</button>
    </div>
  </div>

  <div id="app-container">
    <header>
      <div style="font-weight:bold" id="display-nome">Chat</div>
    </header>

    <div id="mensagens"></div>

    <footer>
      <div class="input-wrapper">
        <input type="text" id="campo-texto" placeholder="Mensagem" autocomplete="off">
      </div>
      <button class="btn-action" id="btn-enviar">➤</button>
    </footer>
  </div>

  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
  <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

  <script>
    // 1. SOLUÇÃO DEFINITIVA PARA O RODAPÉ NO ANDROID
    const appContainer = document.getElementById('app-container');

    function ajustarLayout() {
      // O VisualViewport indica a área REALMENTE visível (descontando o teclado)
      if (window.visualViewport) {
        const viewportHeight = window.visualViewport.height;
        appContainer.style.height = viewportHeight + 'px';
        
        // Garante que a página não role para baixo, escondendo o topo
        window.scrollTo(0, 0);
      }
    }

    // Escuta mudanças no tamanho da tela (quando o teclado sobe/desce)
    if (window.visualViewport) {
      window.visualViewport.addEventListener('resize', ajustarLayout);
      window.visualViewport.addEventListener('scroll', ajustarLayout);
    }
    window.addEventListener('load', ajustarLayout);

    // 2. LÓGICA DO FIREBASE
    const firebaseConfig = { databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();
    const myId = Math.random().toString(36).substring(7);
    let myName = "";

    document.getElementById("btn-entrar").onclick = () => {
      myName = document.getElementById("input-nome").value.trim();
      if (!myName) return alert("Digite seu nome!");
      document.getElementById("display-nome").innerText = myName;
      document.getElementById("tela-login").style.display = "none";
      iniciarChat();
    };

    function enviarMensagem() {
      const input = document.getElementById("campo-texto");
      if (!input.value.trim()) return;
      db.ref("mensagens").push({
        senderId: myId,
        nome: myName,
        texto: input.value.trim(),
        timestamp: Date.now()
      });
      input.value = "";
      // Após enviar, foca novamente para o teclado não fechar no Android
      input.focus();
    }

    document.getElementById("btn-enviar").onclick = enviarMensagem;
    document.getElementById("campo-texto").onkeypress = (e) => { if(e.key === 'Enter') enviarMensagem(); };

    function iniciarChat() {
      const areaMsgs = document.getElementById("mensagens");
      db.ref("mensagens").limitToLast(30).on("child_added", (snap) => {
        const msg = snap.val();
        const div = document.createElement("div");
        div.className = `msg ${msg.senderId === myId ? 'sent' : 'received'}`;
        div.innerHTML = `<small style="display:block;font-size:10px;font-weight:bold;">${msg.nome}</small>${msg.texto}`;
        areaMsgs.appendChild(div);
        areaMsgs.scrollTop = areaMsgs.scrollHeight;
      });
    }
  </script>
</body>
</html>
