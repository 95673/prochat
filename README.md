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
