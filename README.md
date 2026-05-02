

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
    }

    /* RESET E ESTRUTURA BASE */
    * { box-sizing: border-box; margin: 0; padding: 0; }

    html, body {
      height: 100%;
      width: 100%;
      overflow: hidden; /* Impede que o corpo da página role */
      font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
      background-color: #d1d7db;
    }

    /* CONTAINER PRINCIPAL USANDO DVH (Dynamic Viewport Height) */
    #app-container {
      display: flex;
      flex-direction: column;
      width: 100%;
      max-width: 500px;
      margin: 0 auto;
      background: var(--bg-light);
      /* O segredo: dvh faz a altura se ajustar ao teclado mobile */
      height: 100vh;
      height: 100dvh; 
      position: relative;
    }

    /* HEADER FIXO NO TOPO */
    header {
      background: var(--primary);
      color: white;
      padding: 10px 15px;
      display: flex;
      align-items: center;
      gap: 12px;
      flex-shrink: 0; /* Impede o header de encolher */
      z-index: 10;
    }

    #user-avatar { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; background: #eee; }

    /* ÁREA DE MENSAGENS (A única que deve rolar) */
    #mensagens {
      flex: 1; /* Ocupa todo o espaço restante */
      overflow-y: auto;
      padding: 15px;
      background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png');
      background-attachment: local;
      display: flex;
      flex-direction: column;
      gap: 8px;
      -webkit-overflow-scrolling: touch;
    }

    /* BALÕES DE MENSAGEM */
    .msg {
      max-width: 85%; padding: 8px 12px; border-radius: 8px;
      font-size: 15px; box-shadow: 0 1px 1px rgba(0,0,0,0.1);
      word-wrap: break-word; line-height: 1.4;
    }
    .sent { align-self: flex-end; background: #e7ffdb; }
    .received { align-self: flex-start; background: white; }

    /* RODAPÉ FIXO NA BASE */
    footer {
      background: #f0f2f5;
      padding: 10px 12px;
      display: flex;
      align-items: center;
      gap: 8px;
      flex-shrink: 0; /* IMPEDIR QUE SUMA */
      padding-bottom: env(safe-area-inset-bottom, 10px);
    }

    .input-wrapper {
      flex: 1;
      background: white;
      border-radius: 20px;
      padding: 5px 15px;
      display: flex;
      align-items: center;
    }

    #campo-texto {
      width: 100%; border: none; padding: 10px 0;
      outline: none; font-size: 16px;
    }

    .btn-action {
      width: 42px; height: 42px; border-radius: 50%;
      border: none; background: var(--secondary);
      color: white; cursor: pointer;
      display: flex; align-items: center; justify-content: center;
    }

    /* TELA DE LOGIN OVERLAY */
    #tela-login {
      position: fixed; inset: 0;
      background: var(--primary);
      z-index: 2000;
      display: flex; align-items: center; justify-content: center;
    }
    .card-login {
      background: white; padding: 30px; border-radius: 20px;
      width: 90%; max-width: 350px; text-align: center;
    }

    #a-escrever { font-size: 12px; color: var(--secondary); padding: 2px 15px; min-height: 18px; }
  </style>
</head>
<body>

  <!-- LOGIN -->
  <div id="tela-login">
    <div class="card-login">
      <h3>ProChat</h3>
      <input type="text" id="input-nome" placeholder="Seu nome..." style="width:100%; padding:12px; margin:15px 0; border:1px solid #ddd; border-radius:8px;">
      <button id="btn-entrar" style="width:100%; padding:12px; background:var(--accent); color:white; border:none; border-radius:8px; font-weight:bold; cursor:pointer;">ENTRAR</button>
    </div>
  </div>

  <!-- APP -->
  <div id="app-container">
    <header>
      <img src="https://ui-avatars.com/api/?name=Chat&background=075e54&color=fff" id="user-avatar">
      <div style="flex:1">
        <div id="display-nome" style="font-weight:bold">Nome</div>
        <div style="font-size:12px; opacity:0.8">Online</div>
      </div>
    </header>

    <div id="mensagens">
      <!-- Mensagens aparecerão aqui -->
    </div>

    <div id="a-escrever"></div>

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
    // Configuração Firebase (Certifique-se de que sua URL está correta)
    const firebaseConfig = { databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com/" };
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const myId = Math.random().toString(36).substring(7);
    let myName = "";

    // Lógica de Entrada
    document.getElementById("btn-entrar").onclick = () => {
      const nomeInput = document.getElementById("input-nome").value.trim();
      if (!nomeInput) return alert("Digite seu nome!");
      
      myName = nomeInput;
      document.getElementById("display-nome").innerText = myName;
      document.getElementById("tela-login").style.display = "none";
      
      iniciarChat();
    };

    function enviarMensagem() {
      const input = document.getElementById("campo-texto");
      const texto = input.value.trim();
      if (!texto) return;

      db.ref("mensagens").push({
        senderId: myId,
        nome: myName,
        texto: texto,
        timestamp: Date.now()
      });
      input.value = "";
      db.ref("digitando/" + myId).remove();
    }

    document.getElementById("btn-enviar").onclick = enviarMensagem;
    document.getElementById("campo-texto").onkeypress = (e) => { if(e.key === 'Enter') enviarMensagem(); };

    // Detectar Digitante
    let typingTimer;
    document.getElementById("campo-texto").oninput = () => {
      db.ref("digitando/" + myId).set({ nome: myName });
      clearTimeout(typingTimer);
      typingTimer = setTimeout(() => db.ref("digitando/" + myId).remove(), 2000);
    };

    db.ref("digitando").on("value", (snap) => {
      const data = snap.val();
      const list = data ? Object.values(data).filter(u => u.nome !== myName).map(u => u.nome) : [];
      document.getElementById("a-escrever").innerText = list.length > 0 ? list[0] + " está digitando..." : "";
    });

    function iniciarChat() {
      const areaMsgs = document.getElementById("mensagens");
      
      db.ref("mensagens").limitToLast(50).on("child_added", (snap) => {
        const msg = snap.val();
        const isMe = msg.senderId === myId;
        
        const div = document.createElement("div");
        div.className = `msg ${isMe ? 'sent' : 'received'}`;
        div.innerHTML = `
          <small style="display:block; font-size:10px; color:var(--secondary); font-weight:bold">${isMe ? 'Você' : msg.nome}</small>
          ${msg.texto}
          <div style="font-size:10px; text-align:right; color:#999; margin-top:4px">${new Date(msg.timestamp).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</div>
        `;
        
        areaMsgs.appendChild(div);
        // Garante que role para o final
        areaMsgs.scrollTop = areaMsgs.scrollHeight;
      });
    }

    // Correção extra para teclados mobile que empurram a tela
    window.visualViewport.addEventListener('resize', () => {
        document.getElementById("app-container").style.height = window.visualViewport.height + "px";
        document.body.scrollTop = 0; 
        window.scrollTo(0, 0);
    });
  </script>
</body>
</html>
