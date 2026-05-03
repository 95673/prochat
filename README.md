<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>ProChat SuperApp | D4n@s</title>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500;600;700&display=swap');
    :root {
      --primary: #075e54;
      --secondary: #128c7e;
      --accent: #25d366;
      --bg-light: #efe7de;
      --bg-app: #f0f2f5;
      --white: #ffffff;
      --gray: #667781;
      --light-gray: #e9edef;
      --danger: #e74c3c;
      --shadow: 0 2px 10px rgba(0,0,0,0.1);
    }
    * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
    html, body { height: 100%; font-family: 'Poppins', sans-serif; background: #d1d7db; overflow: hidden; }

    #toast {
      position: fixed; bottom: 80px; left: 50%;
      transform: translateX(-50%) translateY(20px);
      background: rgba(0,0,0,0.8); color: white;
      padding: 10px 20px; border-radius: 20px;
      font-size: 13px; z-index: 9999;
      opacity: 0; transition: all 0.3s ease;
      pointer-events: none; white-space: nowrap;
    }
    #toast.show { opacity: 1; transform: translateX(-50%) translateY(0); }

    #app { position: fixed; top: 0; left: 0; right: 0; bottom: 0; display: flex; flex-direction: column; max-width: 500px; margin: 0 auto; background: var(--bg-app); overflow: hidden; }

    /* LOGIN */
    #tela-login {
      position: fixed; inset: 0;
      background: linear-gradient(160deg, var(--primary) 0%, var(--secondary) 100%);
      display: flex; align-items: center; justify-content: center;
      z-index: 3000; transition: all 0.5s ease;
    }
    .card-login {
      background: white; padding: 35px 25px;
      border-radius: 24px; width: 90%; max-width: 360px;
      text-align: center; box-shadow: 0 20px 60px rgba(0,0,0,0.3);
      animation: slideUp 0.6s ease;
    }
    @keyframes slideUp { from { transform: translateY(40px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
    .login-logo { font-size: 52px; margin-bottom: 5px; }
    .card-login h2 { color: var(--primary); font-size: 24px; font-weight: 700; margin-bottom: 5px; }
    .card-login p { color: var(--gray); font-size: 13px; margin-bottom: 25px; }

    #btn-google {
      width: 100%; padding: 14px;
      background: white; border: 2px solid var(--light-gray);
      border-radius: 12px; color: #333;
      font-weight: 600; font-size: 15px; cursor: pointer;
      font-family: 'Poppins', sans-serif;
      display: flex; align-items: center; justify-content: center; gap: 12px;
      transition: all 0.2s; box-shadow: var(--shadow);
    }
    #btn-google:active { transform: scale(0.97); }
    #btn-google:hover { border-color: var(--accent); }
    .google-icon { font-size: 22px; }

    .login-divider {
      display: flex; align-items: center; gap: 10px;
      margin: 15px 0; color: var(--gray); font-size: 12px;
    }
    .login-divider::before, .login-divider::after {
      content: ''; flex: 1; height: 1px; background: var(--light-gray);
    }

    .login-input {
      width: 100%; padding: 13px 15px; margin: 8px 0;
      border: 2px solid var(--light-gray); border-radius: 12px;
      font-size: 15px; font-family: 'Poppins', sans-serif;
      outline: none; transition: border 0.2s;
    }
    .login-input:focus { border-color: var(--accent); }
    .login-select {
      width: 100%; padding: 13px 15px; margin: 8px 0;
      border: 2px solid var(--light-gray); border-radius: 12px;
      font-size: 15px; font-family: 'Poppins', sans-serif;
      outline: none; background: white;
    }
    #btn-entrar {
      width: 100%; padding: 14px;
      background: linear-gradient(135deg, var(--accent), var(--secondary));
      border: none; border-radius: 12px; color: white;
      font-weight: 700; font-size: 15px; cursor: pointer;
      font-family: 'Poppins', sans-serif; margin-top: 5px;
      box-shadow: 0 4px 15px rgba(37,211,102,0.4);
      transition: transform 0.2s;
    }
    #btn-entrar:active { transform: scale(0.97); }

    /* HEADER */
    header { background: var(--primary); color: white; padding: 12px 18px; display: flex; align-items: center; gap: 12px; flex-shrink: 0; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
    .header-avatar-wrap { position: relative; }
    #user-avatar { width: 40px; height: 40px; border-radius: 50%; object-fit: cover; border: 2px solid var(--accent); }
    .status-dot { width: 11px; height: 11px; border: 2px solid var(--primary); border-radius: 50%; position: absolute; bottom: 1px; right: 1px; background: var(--accent); }
    .header-info { flex: 1; }
    .header-name { font-weight: 600; font-size: 16px; }
    .header-status { font-size: 11px; opacity: 0.8; }
    .header-actions { display: flex; gap: 6px; }
    .btn-header { width: 38px; height: 38px; border-radius: 50%; border: none; background: rgba(255,255,255,0.15); color: white; cursor: pointer; font-size: 16px; display: flex; align-items: center; justify-content: center; transition: background 0.2s; }
    .btn-header:active { background: rgba(255,255,255,0.3); }

    /* SEARCH */
    #barra-pesquisa { background: var(--secondary); padding: 8px 12px; display: flex; gap: 8px; align-items: center; flex-shrink: 0; }
    #input-pesquisa { flex: 1; padding: 9px 15px; border-radius: 20px; border: none; outline: none; font-size: 14px; font-family: 'Poppins', sans-serif; background: rgba(255,255,255,0.9); }
    #btn-pesquisa { background: var(--accent); border: none; border-radius: 50%; width: 38px; height: 38px; color: white; font-size: 16px; cursor: pointer; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }

    /* TABS */
    .tab-content { display: none; flex: 1; flex-direction: column; overflow: hidden; }
    .tab-content.active { display: flex; }

    /* CHAT */
    #area-video { display: none; background: #000; height: 160px; padding: 8px; justify-content: center; flex-shrink: 0; }
    #v-local { height: 100%; border-radius: 10px; border: 2px solid var(--accent); }
    #mensagens { flex: 1; overflow-y: auto; padding: 12px; background-color: var(--bg-light); background-image: url('https://user-images.githubusercontent.com/15075759/28719144-86dc0f70-73b1-11e7-911d-60d70fcded21.png'); display: flex; flex-direction: column; gap: 5px; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; }
    .msg { max-width: 80%; padding: 8px 12px; border-radius: 12px; font-size: 14px; box-shadow: 0 1px 2px rgba(0,0,0,0.12); animation: popIn 0.25s ease-out; word-wrap: break-word; }
    @keyframes popIn { from { transform: scale(0.85); opacity: 0; } to { transform: scale(1); opacity: 1; } }
    .sent { align-self: flex-end; background: #e7ffdb; border-top-right-radius: 3px; }
    .received { align-self: flex-start; background: white; border-top-left-radius: 3px; }
    .msg-sender { font-size: 10px; font-weight: 600; color: var(--secondary); margin-bottom: 2px; display: block; }
    .msg-info { font-size: 10px; color: #999; display: flex; justify-content: flex-end; align-items: center; gap: 3px; margin-top: 3px; }
    .tick { font-size: 12px; color: #aaa; }
    .tick.entregue { color: #4fc3f7; }
    .tick.lido { color: var(--accent); }
    #a-escrever { padding: 4px 15px; font-size: 12px; color: var(--secondary); font-style: italic; min-height: 22px; flex-shrink: 0; background: var(--bg-light); }

    /* FOOTER CHAT */
    .chat-footer { background: var(--bg-app); padding: 8px 12px; display: flex; align-items: center; gap: 8px; flex-shrink: 0; }
    .input-msg-wrap { flex: 1; background: white; border-radius: 25px; padding: 4px 12px; display: flex; align-items: center; gap: 6px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
    #campo-texto { flex: 1; border: none; padding: 9px 0; outline: none; font-size: 15px; font-family: 'Poppins', sans-serif; background: transparent; }
    .btn-colar { background: none; border: none; font-size: 18px; cursor: pointer; padding: 2px; color: var(--gray); }
    .btn-round { width: 44px; height: 44px; border-radius: 50%; border: none; background: var(--accent); color: white; cursor: pointer; display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; box-shadow: 0 2px 8px rgba(37,211,102,0.4); transition: transform 0.2s; }
    .btn-round:active { transform: scale(0.9); }
    .btn-round.gray { background: #e3e6eb; color: #54656f; box-shadow: none; }

    /* MENU */
    #menu-opcoes { display: none; position: fixed; background: white; border-radius: 14px; box-shadow: 0 8px 30px rgba(0,0,0,0.2); z-index: 9999; overflow: hidden; min-width: 180px; }
    .menu-item { padding: 13px 18px; font-size: 14px; cursor: pointer; border-bottom: 1px solid #f5f5f5; font-family: 'Poppins', sans-serif; display: flex; align-items: center; gap: 10px; transition: background 0.15s; }
    .menu-item:hover { background: #f8f8f8; }
    .menu-item:last-child { border-bottom: none; }
    .menu-item.danger { color: var(--danger); }
    #overlay-menu { display: none; position: fixed; inset: 0; z-index: 9998; }

    /* VENDAS */
    .section-content { flex: 1; overflow-y: auto; padding: 12px; -webkit-overflow-scrolling: touch; }
    .section-header { background: var(--primary); color: white; padding: 12px 18px; font-weight: 600; font-size: 16px; flex-shrink: 0; display: flex; align-items: center; gap: 10px; }
    .card { background: white; border-radius: 14px; padding: 15px; margin-bottom: 12px; box-shadow: var(--shadow); }
    .card h3 { color: var(--primary); font-size: 15px; margin-bottom: 10px; }
    .form-input { width: 100%; padding: 11px 13px; margin: 6px 0; border: 2px solid var(--light-gray); border-radius: 10px; font-size: 14px; font-family: 'Poppins', sans-serif; outline: none; transition: border 0.2s; }
    .form-input:focus { border-color: var(--accent); }
    .btn-primary { width: 100%; padding: 12px; background: linear-gradient(135deg, var(--accent), var(--secondary)); border: none; border-radius: 10px; color: white; font-weight: 600; font-size: 14px; cursor: pointer; font-family: 'Poppins', sans-serif; margin-top: 6px; transition: transform 0.2s; }
    .btn-primary:active { transform: scale(0.97); }
    .produtos-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 10px; }
    .produto-card { background: white; border-radius: 12px; overflow: hidden; box-shadow: var(--shadow); animation: popIn 0.3s ease; }
    .produto-img { width: 100%; height: 100px; object-fit: cover; background: var(--light-gray); display: flex; align-items: center; justify-content: center; font-size: 40px; }
    .produto-img img { width: 100%; height: 100%; object-fit: cover; }
    .produto-info { padding: 10px; }
    .produto-nome { font-size: 13px; font-weight: 600; color: #333; }
    .produto-preco { font-size: 14px; font-weight: 700; color: var(--accent); margin: 3px 0; }
    .produto-btns { display: flex; gap: 5px; margin-top: 6px; }
    .btn-comprar { flex: 1; padding: 7px; background: var(--accent); border: none; border-radius: 8px; color: white; font-size: 11px; font-weight: 600; cursor: pointer; font-family: 'Poppins', sans-serif; }
    .btn-falar { flex: 1; padding: 7px; background: var(--primary); border: none; border-radius: 8px; color: white; font-size: 11px; font-weight: 600; cursor: pointer; font-family: 'Poppins', sans-serif; }

    /* TRABALHO */
    .todo-item { background: white; border-radius: 10px; padding: 12px 15px; margin-bottom: 8px; display: flex; align-items: center; gap: 10px; box-shadow: var(--shadow); animation: popIn 0.25s ease; }
    .todo-check { width: 22px; height: 22px; border-radius: 50%; border: 2px solid var(--accent); cursor: pointer; display: flex; align-items: center; justify-content: center; flex-shrink: 0; transition: all 0.2s; background: white; }
    .todo-check.done { background: var(--accent); border-color: var(--accent); }
    .todo-text { flex: 1; font-size: 14px; }
    .todo-text.done { text-decoration: line-through; color: var(--gray); }
    .btn-delete { background: none; border: none; color: var(--danger); font-size: 18px; cursor: pointer; }
    .notas-area { width: 100%; min-height: 120px; padding: 12px; border: 2px solid var(--light-gray); border-radius: 10px; font-size: 14px; font-family: 'Poppins', sans-serif; resize: none; outline: none; margin-top: 6px; transition: border 0.2s; }
    .notas-area:focus { border-color: var(--accent); }

    /* FEED */
    .post-card { background: white; border-radius: 14px; margin-bottom: 12px; overflow: hidden; box-shadow: var(--shadow); animation: popIn 0.3s ease; }
    .post-header { padding: 12px 15px; display: flex; align-items: center; gap: 10px; }
    .post-avatar { width: 38px; height: 38px; border-radius: 50%; background: var(--accent); display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; overflow: hidden; }
    .post-avatar img { width: 100%; height: 100%; object-fit: cover; }
    .post-autor { font-weight: 600; font-size: 14px; }
    .post-tempo { font-size: 11px; color: var(--gray); }
    .post-texto { padding: 0 15px 12px; font-size: 14px; line-height: 1.5; }
    .post-img { width: 100%; max-height: 250px; object-fit: cover; }
    .post-actions { padding: 8px 15px; display: flex; gap: 5px; border-top: 1px solid var(--light-gray); }
    .btn-post { flex: 1; padding: 9px 5px; background: none; border: none; font-size: 12px; cursor: pointer; font-family: 'Poppins', sans-serif; color: var(--gray); border-radius: 8px; display: flex; align-items: center; justify-content: center; gap: 5px; transition: background 0.2s; }
    .btn-post:active { background: var(--light-gray); }
    .btn-post.curtido { color: var(--danger); }
    .comentarios { padding: 0 15px 10px; display: none; }
    .comentario-input-wrap { display: flex; gap: 8px; margin-top: 8px; }
    .comentario-input { flex: 1; padding: 8px 12px; border: 1px solid var(--light-gray); border-radius: 20px; font-size: 13px; font-family: 'Poppins', sans-serif; outline: none; }
    .btn-comentar { background: var(--accent); border: none; border-radius: 50%; width: 34px; height: 34px; color: white; cursor: pointer; font-size: 14px; display: flex; align-items: center; justify-content: center; }
    .comentario-item { background: var(--light-gray); border-radius: 10px; padding: 8px 12px; margin-top: 6px; font-size: 13px; }
    .comentario-autor { font-weight: 600; font-size: 11px; color: var(--secondary); }

    /* NAV */
    nav { background: white; display: flex; border-top: 1px solid var(--light-gray); flex-shrink: 0; box-shadow: 0 -2px 10px rgba(0,0,0,0.08); }
    .nav-btn { flex: 1; padding: 10px 5px; background: none; border: none; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 3px; font-family: 'Poppins', sans-serif; font-size: 10px; color: var(--gray); transition: color 0.2s; position: relative; }
    .nav-btn .nav-icon { font-size: 22px; }
    .nav-btn.active { color: var(--primary); }
    .nav-btn.active::after { content: ''; position: absolute; top: 0; left: 20%; right: 20%; height: 3px; background: var(--primary); border-radius: 0 0 4px 4px; }

    .watermark { text-align: center; font-size: 10px; color: rgba(0,0,0,0.07); padding: 2px; letter-spacing: 2px; flex-shrink: 0; background: var(--bg-app); }
    ::-webkit-scrollbar { width: 4px; }
    ::-webkit-scrollbar-thumb { background: rgba(0,0,0,0.15); border-radius: 4px; }
  </style>
</head>
<body>

<div id="toast"></div>

<!-- LOGIN -->
<div id="tela-login">
  <div class="card-login">
    <div class="login-logo">💬</div>
    <h2>ProChat</h2>
    <p>SuperApp • Chat • Vendas • Trabalho • Feed</p>

    <!-- BOTÃO GOOGLE -->
    <button id="btn-google">
      <span class="google-icon">🔵</span>
      Entrar com Google
    </button>

    <div class="login-divider">ou entra manualmente</div>

    <select id="select-estado" class="login-select">
      <option value="Online">🟢 Online</option>
      <option value="Ocupado">🟡 Ocupado</option>
      <option value="Ausente">🔴 Ausente</option>
    </select>
    <button id="btn-entrar">ENTRAR COM NOME DO GOOGLE</button>
  </div>
</div>

<!-- APP -->
<div id="app">
  <header>
    <div class="header-avatar-wrap">
      <img src="" id="user-avatar">
      <div id="status-dot" class="status-dot"></div>
    </div>
    <div class="header-info">
      <div id="display-nome" class="header-name">ProChat</div>
      <div id="display-estado" class="header-status">Online</div>
    </div>
    <div class="header-actions">
      <button class="btn-header" onclick="ativarVideo()">🎥</button>
      <button class="btn-header" onclick="ativarVoz()">📞</button>
      <button class="btn-header" onclick="sair()" title="Sair">🚪</button>
    </div>
  </header>

  <div id="barra-pesquisa">
    <input type="text" id="input-pesquisa" placeholder="🔍 Procurar amigo...">
    <button id="btn-pesquisa" onclick="iniciarConversa()">➤</button>
  </div>

  <!-- TAB CHAT -->
  <div id="tab-chat" class="tab-content active">
    <div id="area-video">
      <video id="v-local" autoplay muted playsinline></video>
    </div>
    <div id="mensagens">
      <div style="text-align:center; padding:20px; color:#999; font-size:13px;">
        👆 Procura um amigo para começar!
      </div>
    </div>
    <div id="a-escrever"></div>
    <div class="chat-footer">
      <button class="btn-round gray" onclick="abrirAnexo()" style="font-size:22px; font-weight:bold;">+</button>
      <div class="input-msg-wrap">
        <input type="text" id="campo-texto" placeholder="Mensagem...">
        <button class="btn-colar" onclick="colarTexto()">📋</button>
      </div>
      <button class="btn-round" id="btn-enviar">➤</button>
    </div>
  </div>

  <!-- TAB VENDAS -->
  <div id="tab-vendas" class="tab-content" style="flex-direction:column;">
    <div class="section-header">🛍️ Marketplace</div>
    <div class="section-content">
      <div class="card">
        <h3>➕ Adicionar Produto</h3>
        <input type="text" id="prod-nome" class="form-input" placeholder="Nome do produto">
        <input type="text" id="prod-preco" class="form-input" placeholder="Preço (ex: 500 MT)">
        <input type="text" id="prod-img" class="form-input" placeholder="URL da imagem (opcional)">
        <button class="btn-primary" onclick="adicionarProduto()">📦 Publicar Produto</button>
      </div>
      <div id="lista-produtos" class="produtos-grid"></div>
    </div>
  </div>

  <!-- TAB TRABALHO -->
  <div id="tab-trabalho" class="tab-content" style="flex-direction:column;">
    <div class="section-header">💼 Área de Trabalho</div>
    <div class="section-content">
      <div class="card">
        <h3>✅ Tarefas</h3>
        <div style="display:flex; gap:8px; margin-bottom:10px;">
          <input type="text" id="input-tarefa" class="form-input" placeholder="Nova tarefa..." style="margin:0; flex:1;">
          <button class="btn-primary" onclick="adicionarTarefa()" style="width:auto; padding:11px 15px; margin:0;">➕</button>
        </div>
        <div id="lista-tarefas"></div>
      </div>
      <div class="card">
        <h3>📝 Notas Rápidas</h3>
        <textarea id="notas-texto" class="notas-area" placeholder="Escreve aqui as tuas notas..." oninput="guardarNotas()"></textarea>
        <div style="font-size:11px; color:var(--gray); margin-top:5px; text-align:right;">✅ Guardado automaticamente</div>
      </div>
    </div>
  </div>

  <!-- TAB FEED -->
  <div id="tab-feed" class="tab-content" style="flex-direction:column;">
    <div class="section-header">📱 Feed Social</div>
    <div class="section-content">
      <div class="card">
        <h3>✍️ Criar Post</h3>
        <textarea id="post-texto" class="notas-area" placeholder="O que estás a pensar?" style="min-height:80px;"></textarea>
        <input type="text" id="post-img" class="form-input" placeholder="URL de imagem (opcional)">
        <button class="btn-primary" onclick="criarPost()">📢 Publicar</button>
      </div>
      <div id="lista-posts"></div>
    </div>
  </div>

  <div class="watermark">D4n@s • ProChat SuperApp</div>

  <nav>
    <button class="nav-btn active" onclick="mudarTab('chat', this)">
      <span class="nav-icon">💬</span>Chat
    </button>
    <button class="nav-btn" onclick="mudarTab('vendas', this)">
      <span class="nav-icon">🛍️</span>Vendas
    </button>
    <button class="nav-btn" onclick="mudarTab('trabalho', this)">
      <span class="nav-icon">💼</span>Trabalho
    </button>
    <button class="nav-btn" onclick="mudarTab('feed', this)">
      <span class="nav-icon">📱</span>Feed
    </button>
  </nav>
</div>

<!-- MENU -->
<div id="menu-opcoes">
  <div class="menu-item" id="opcao-copiar">📋 Copiar</div>
  <div class="menu-item" id="opcao-reencaminhar">↪️ Reencaminhar</div>
  <div class="menu-item" id="opcao-partilhar">🔗 Partilhar</div>
  <div class="menu-item danger" id="opcao-apagar">🗑️ Apagar</div>
</div>
<div id="overlay-menu"></div>

<!-- FIREBASE -->
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

<script>
// ===== FIREBASE CONFIG =====
firebase.initializeApp({
  apiKey: "AIzaSyAxUXJAnzEgZgSpirHpDSWrYeyfG7Q-aWU",
  authDomain: "meu-chat-b887c.firebaseapp.com",
  databaseURL: "https://meu-chat-b887c-default-rtdb.firebaseio.com",
  projectId: "meu-chat-b887c",
  storageBucket: "meu-chat-b887c.firebasestorage.app",
  messagingSenderId: "69015228954",
  appId: "1:69015228954:web:2bdf4417dd085e128077a3"
});

const db = firebase.database();
const auth = firebase.auth();
const provider = new firebase.auth.GoogleAuthProvider();

const myId = Math.random().toString(36).substring(7);
let myName = "";
let myPhoto = "";
let typingTimer;
let salaActual = null;
let salaListener = null;
let msgSelecionada = null;
let textoSelecionado = "";

// ===== TOAST =====
function toast(msg) {
  const t = document.getElementById("toast");
  t.textContent = msg;
  t.classList.add("show");
  setTimeout(() => t.classList.remove("show"), 2500);
}

// ===== ALTURA =====
function fixHeight() {
  document.getElementById("app").style.height = window.innerHeight + "px";
}
window.addEventListener("resize", fixHeight);
window.addEventListener("load", fixHeight);

// ===== LOGIN COM GOOGLE =====
document.getElementById("btn-google").onclick = () => {
  auth.signInWithPopup(provider)
    .then((result) => {
      const user = result.user;
      entrarNoApp(user.displayName, user.photoURL || "", document.getElementById("select-estado").value);
    })
    .catch((error) => {
      toast("⚠️ Erro: " + error.message);
    });
};

// ===== LOGIN MANUAL (usa nome do Google se já logado) =====
document.getElementById("btn-entrar").onclick = () => {
  const user = auth.currentUser;
  if (user) {
    entrarNoApp(user.displayName, user.photoURL || "", document.getElementById("select-estado").value);
  } else {
    toast("⚠️ Carrega em 'Entrar com Google' primeiro!");
  }
};

// ===== VERIFICAR SE JÁ ESTÁ LOGADO =====
auth.onAuthStateChanged((user) => {
  if (user) {
    document.getElementById("btn-entrar").textContent = "ENTRAR COMO " + user.displayName.split(" ")[0].toUpperCase();
  }
});

function entrarNoApp(nome, foto, estado) {
  myName = nome;
  myPhoto = foto || "https://ui-avatars.com/api/?name=" + encodeURIComponent(nome) + "&background=075e54&color=fff&size=150";
  const cores = { Online: "#25d366", Ocupado: "#f0c000", Ausente: "#e74c3c" };
  document.getElementById("display-nome").innerText = myName;
  document.getElementById("display-estado").innerText = estado;
  document.getElementById("status-dot").style.background = cores[estado];
  document.getElementById("user-avatar").src = myPhoto;
  db.ref("utilizadores/" + myName).set({ nome: myName, estado, online: true, foto: myPhoto });
  const tela = document.getElementById("tela-login");
  tela.style.opacity = "0";
  tela.style.transform = "scale(1.05)";
  setTimeout(() => tela.style.display = "none", 500);
  carregarDados();
  toast("👋 Bem-vindo, " + myName.split(" ")[0] + "!");
}

function sair() {
  if (confirm("Queres sair do ProChat?")) {
    auth.signOut().then(() => {
      db.ref("utilizadores/" + myName).update({ online: false });
      location.reload();
    });
  }
}

// ===== TABS =====
function mudarTab(tab, btn) {
  document.querySelectorAll(".tab-content").forEach(t => t.classList.remove("active"));
  document.querySelectorAll(".nav-btn").forEach(b => b.classList.remove("active"));
  document.getElementById("tab-" + tab).classList.add("active");
  btn.classList.add("active");
  document.getElementById("barra-pesquisa").style.display = tab === "chat" ? "flex" : "none";
}

// ===== CHAT =====
function iniciarConversa() {
  const amigo = document.getElementById("input-pesquisa").value.trim();
  if (!amigo) { toast("⚠️ Escreve o nome do amigo!"); return; }
  if (amigo === myName) { toast("😄 Não podes falar contigo mesmo!"); return; }
  const nomes = [myName, amigo].sort();
  salaActual = "sala-" + nomes[0] + "-" + nomes[1];
  document.getElementById("display-nome").innerText = "💬 " + amigo;
  document.getElementById("input-pesquisa").value = "";
  document.getElementById("mensagens").innerHTML = "";
  if (salaListener) db.ref(salaListener).off();
  salaListener = salaActual;
  db.ref(salaActual).limitToLast(50).on("child_added", carregarMensagem);
  db.ref(salaActual).on("child_removed", (snap) => {
    const el = document.getElementById("msg-" + snap.key);
    if (el) el.remove();
  });
  db.ref(salaActual).on("child_changed", (snap) => {
    const data = snap.val();
    const msgDiv = document.getElementById("msg-" + snap.key);
    if (!msgDiv || data.senderId !== myId) return;
    const tickEl = msgDiv.querySelector(".tick");
    if (!tickEl) return;
    if (data.lido) { tickEl.className = "tick lido"; tickEl.innerText = "✓✓"; }
    else if (data.entregue) { tickEl.className = "tick entregue"; tickEl.innerText = "✓✓"; }
  });
  toast("💬 Conversa com " + amigo + " aberta!");
}

function carregarMensagem(snap) {
  const data = snap.val();
  const area = document.getElementById("mensagens");
  const isMe = data.senderId === myId;
  if (!isMe) db.ref(salaActual + "/" + snap.key).update({ lido: true });
  const div = document.createElement("div");
  div.className = "msg " + (isMe ? "sent" : "received");
  div.id = "msg-" + snap.key;
  let ticks = "";
  if (isMe) {
    if (data.lido) ticks = `<span class="tick lido">✓✓</span>`;
    else if (data.entregue) ticks = `<span class="tick entregue">✓✓</span>`;
    else ticks = `<span class="tick">✓</span>`;
  }
  div.innerHTML = `
    <span class="msg-sender">${isMe ? "Você" : data.nome}</span>
    ${data.texto}
    <span class="msg-info">
      ${new Date(data.timestamp).toLocaleTimeString([], {hour:"2-digit", minute:"2-digit"})}
      ${ticks}
    </span>
  `;
  let pressTimer;
  const iniciar = (e) => { pressTimer = setTimeout(() => abrirMenu(e, snap, isMe), 600); };
  const cancelar = () => clearTimeout(pressTimer);
  div.addEventListener("mousedown", iniciar);
  div.addEventListener("mouseup", cancelar);
  div.addEventListener("touchstart", iniciar);
  div.addEventListener("touchend", cancelar);
  div.addEventListener("touchmove", cancelar);
  area.appendChild(div);
  area.scrollTop = area.scrollHeight;
}

function enviar() {
  if (!salaActual) { toast("⚠️ Procura um amigo primeiro!"); return; }
  const input = document.getElementById("campo-texto");
  const texto = input.value.trim();
  if (!texto) return;
  db.ref(salaActual).push({
    senderId: myId, nome: myName,
    texto, timestamp: Date.now(),
    entregue: true, lido: false
  });
  db.ref("digitando/" + myId).remove();
  input.value = "";
}

document.getElementById("btn-enviar").onclick = enviar;
document.getElementById("campo-texto").onkeypress = (e) => { if (e.key === "Enter") enviar(); };

document.getElementById("campo-texto").oninput = () => {
  if (!salaActual) return;
  db.ref("digitando/" + myId).set({ nome: myName });
  clearTimeout(typingTimer);
  typingTimer = setTimeout(() => db.ref("digitando/" + myId).remove(), 2000);
};

db.ref("digitando").on("value", (snap) => {
  const dados = snap.val();
  const indicador = document.getElementById("a-escrever");
  if (!dados) { indicador.innerText = ""; return; }
  const outros = Object.values(dados).filter(d => d.nome !== myName).map(d => d.nome);
  if (outros.length === 0) indicador.innerText = "";
  else if (outros.length === 1) indicador.innerText = outros[0] + " está a escrever...";
  else indicador.innerText = "Vários estão a escrever...";
});

async function colarTexto() {
  try {
    const texto = await navigator.clipboard.readText();
    document.getElementById("campo-texto").value += texto;
    toast("📋 Texto colado!");
  } catch { toast("⚠️ Não foi possível colar"); }
}

function abrirAnexo() { toast("📎 Funcionalidade em breve!"); }

// ===== MENU =====
const menu = document.getElementById("menu-opcoes");
const overlay = document.getElementById("overlay-menu");

function abrirMenu(e, snap, isMe) {
  msgSelecionada = { snap, isMe };
  textoSelecionado = snap.val().texto;
  document.getElementById("opcao-apagar").style.display = isMe ? "flex" : "none";
  const clientX = e.clientX || (e.touches && e.touches[0].clientX) || 100;
  const clientY = e.clientY || (e.touches && e.touches[0].clientY) || 200;
  const x = Math.min(clientX, window.innerWidth - 200);
  const y = Math.min(clientY, window.innerHeight - 180);
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

document.getElementById("opcao-copiar").onclick = () => {
  navigator.clipboard.writeText(textoSelecionado)
    .then(() => toast("✅ Mensagem copiada!"))
    .catch(() => toast("⚠️ Erro ao copiar"));
  fecharMenu();
};

document.getElementById("opcao-reencaminhar").onclick = () => {
  document.getElementById("campo-texto").value = textoSelecionado;
  mudarTab("chat", document.querySelectorAll(".nav-btn")[0]);
  fecharMenu();
  toast("↪️ Pronto para reencaminhar!");
};

document.getElementById("opcao-partilhar").onclick = () => {
  if (navigator.share) {
    navigator.share({ text: textoSelecionado }).catch(() => {});
  } else {
    navigator.clipboard.writeText(textoSelecionado);
    toast("📋 Copiado!");
  }
  fecharMenu();
};

document.getElementById("opcao-apagar").onclick = () => {
  if (confirm("Apagar esta mensagem para todos?")) {
    db.ref(salaActual + "/" + msgSelecionada.snap.key).remove();
    toast("🗑️ Mensagem apagada!");
  }
  fecharMenu();
};

// ===== VÍDEO =====
function ativarVideo() {
  const area = document.getElementById("area-video");
  area.style.display = area.style.display === "none" ? "flex" : "none";
  if (area.style.display === "flex") {
    navigator.mediaDevices.getUserMedia({ video: true, audio: true })
      .then(s => { document.getElementById("v-local").srcObject = s; toast("🎥 Câmara activada!"); })
      .catch(err => toast("⚠️ " + err.message));
  } else { toast("🎥 Câmara desactivada!"); }
}

function ativarVoz() {
  navigator.mediaDevices.getUserMedia({ video: false, audio: true })
    .then(() => toast("🎙️ Microfone activado!"))
    .catch(err => toast("⚠️ " + err.message));
}

// ===== VENDAS =====
function adicionarProduto() {
  const nome = document.getElementById("prod-nome").value.trim();
  const preco = document.getElementById("prod-preco").value.trim();
  const img = document.getElementById("prod-img").value.trim();
  if (!nome || !preco) { toast("⚠️ Preenche nome e preço!"); return; }
  const produtos = JSON.parse(localStorage.getItem("produtos") || "[]");
  produtos.push({ id: Date.now(), nome, preco, img });
  localStorage.setItem("produtos", JSON.stringify(produtos));
  document.getElementById("prod-nome").value = "";
  document.getElementById("prod-preco").value = "";
  document.getElementById("prod-img").value = "";
  renderProdutos();
  toast("📦 Produto publicado!");
}

function renderProdutos() {
  const produtos = JSON.parse(localStorage.getItem("produtos") || "[]");
  const lista = document.getElementById("lista-produtos");
  lista.innerHTML = produtos.length === 0
    ? '<div style="grid-column:1/-1; text-align:center; color:#aaa; font-size:13px; padding:20px;">Nenhum produto ainda</div>'
    : "";
  produtos.forEach(p => {
    const div = document.createElement("div");
    div.className = "produto-card";
    div.innerHTML = `
      <div class="produto-img">
        ${p.img ? `<img src="${p.img}" onerror="this.parentElement.innerHTML='📦'">` : "📦"}
      </div>
      <div class="produto-info">
        <div class="produto-nome">${p.nome}</div>
        <div class="produto-preco">${p.preco}</div>
        <div class="produto-btns">
          <button class="btn-comprar" onclick="comprarProduto('${p.nome}')">🛒 Comprar</button>
          <button class="btn-falar" onclick="falarVendedor('${p.nome}')">💬 Falar</button>
        </div>
      </div>
    `;
    lista.appendChild(div);
  });
}

function comprarProduto(nome) { toast("🛒 Interesse registado: " + nome); }
function falarVendedor(nome) {
  document.getElementById("campo-texto").value = "Olá! Tenho interesse no produto: " + nome;
  mudarTab("chat", document.querySelectorAll(".nav-btn")[0]);
  toast("💬 Mensagem preparada no chat!");
}

// ===== TRABALHO =====
function adicionarTarefa() {
  const texto = document.getElementById("input-tarefa").value.trim();
  if (!texto) { toast("⚠️ Escreve uma tarefa!"); return; }
  const tarefas = JSON.parse(localStorage.getItem("tarefas") || "[]");
  tarefas.push({ id: Date.now(), texto, done: false });
  localStorage.setItem("tarefas", JSON.stringify(tarefas));
  document.getElementById("input-tarefa").value = "";
  renderTarefas();
  toast("✅ Tarefa adicionada!");
}

document.addEventListener("keypress", (e) => {
  if (e.key === "Enter" && document.activeElement.id === "input-tarefa") adicionarTarefa();
});

function toggleTarefa(id) {
  const tarefas = JSON.parse(localStorage.getItem("tarefas") || "[]");
  const t = tarefas.find(t => t.id == id);
  if (t) t.done = !t.done;
  localStorage.setItem("tarefas", JSON.stringify(tarefas));
  renderTarefas();
}

function apagarTarefa(id) {
  let tarefas = JSON.parse(localStorage.getItem("tarefas") || "[]");
  tarefas = tarefas.filter(t => t.id != id);
  localStorage.setItem("tarefas", JSON.stringify(tarefas));
  renderTarefas();
  toast("🗑️ Tarefa removida!");
}

function renderTarefas() {
  const tarefas = JSON.parse(localStorage.getItem("tarefas") || "[]");
  const lista = document.getElementById("lista-tarefas");
  lista.innerHTML = tarefas.length === 0
    ? '<div style="text-align:center; color:#aaa; font-size:13px; padding:15px;">Nenhuma tarefa ainda</div>'
    : "";
  tarefas.forEach(t => {
    const div = document.createElement("div");
    div.className = "todo-item";
    div.innerHTML = `
      <div class="todo-check ${t.done ? 'done' : ''}" onclick="toggleTarefa(${t.id})">${t.done ? '✓' : ''}</div>
      <span class="todo-text ${t.done ? 'done' : ''}">${t.texto}</span>
      <button class="btn-delete" onclick="apagarTarefa(${t.id})">🗑️</button>
    `;
    lista.appendChild(div);
  });
}

function guardarNotas() {
  localStorage.setItem("notas", document.getElementById("notas-texto").value);
}

// ===== FEED =====
function criarPost() {
  const texto = document.getElementById("post-texto").value.trim();
  const img = document.getElementById("post-img").value.trim();
  if (!texto) { toast("⚠️ Escreve algo!"); return; }
  const posts = JSON.parse(localStorage.getItem("posts") || "[]");
  posts.unshift({ id: Date.now(), autor: myName, foto: myPhoto, texto, img, likes: 0, curtido: false, comentarios: [], timestamp: Date.now() });
  localStorage.setItem("posts", JSON.stringify(posts));
  document.getElementById("post-texto").value = "";
  document.getElementById("post-img").value = "";
  renderPosts();
  toast("📢 Post publicado!");
}

function curtirPost(id) {
  const posts = JSON.parse(localStorage.getItem("posts") || "[]");
  const p = posts.find(p => p.id == id);
  if (p) { p.curtido = !p.curtido; p.likes += p.curtido ? 1 : -1; }
  localStorage.setItem("posts", JSON.stringify(posts));
  renderPosts();
}

function toggleComentarios(id) {
  const sec = document.getElementById("com-" + id);
  if (sec) sec.style.display = sec.style.display === "none" ? "block" : "none";
}

function adicionarComentario(id) {
  const input = document.getElementById("com-input-" + id);
  const texto = input.value.trim();
  if (!texto) return;
  const posts = JSON.parse(localStorage.getItem("posts") || "[]");
  const p = posts.find(p => p.id == id);
  if (p) p.comentarios.push({ autor: myName, texto });
  localStorage.setItem("posts", JSON.stringify(posts));
  input.value = "";
  renderPosts();
  toast("💬 Comentário adicionado!");
}

function partilharPost(texto) {
  if (navigator.share) { navigator.share({ text: texto }).catch(() => {}); }
  else { navigator.clipboard.writeText(texto); toast("📋 Post copiado!"); }
}

function renderPosts() {
  const posts = JSON.parse(localStorage.getItem("posts") || "[]");
  const lista = document.getElementById("lista-posts");
  lista.innerHTML = posts.length === 0
    ? '<div style="text-align:center; color:#aaa; font-size:13px; padding:20px;">Nenhum post ainda!</div>'
    : "";
  posts.forEach(p => {
    const div = document.createElement("div");
    div.className = "post-card";
    div.innerHTML = `
      <div class="post-header">
        <div class="post-avatar">
          ${p.foto ? `<img src="${p.foto}" onerror="this.parentElement.innerText='${p.autor.charAt(0)}'">` : p.autor.charAt(0).toUpperCase()}
        </div>
        <div>
          <div class="post-autor">${p.autor}</div>
          <div class="post-tempo">${new Date(p.timestamp).toLocaleTimeString([], {hour:"2-digit", minute:"2-digit"})}</div>
        </div>
      </div>
      <div class="post-texto">${p.texto}</div>
      ${p.img ? `<img src="${p.img}" class="post-img" onerror="this.style.display='none'">` : ""}
      <div class="post-actions">
        <button class="btn-post ${p.curtido ? 'curtido' : ''}" onclick="curtirPost(${p.id})">${p.curtido ? '❤️' : '🤍'} ${p.likes}</button>
        <button class="btn-post" onclick="toggleComentarios(${p.id})">💬 ${p.comentarios.length}</button>
        <button class="btn-post" onclick="partilharPost('${p.texto.replace(/'/g, "\\'")}')">🔗 Partilhar</button>
      </div>
      <div class="comentarios" id="com-${p.id}">
        ${p.comentarios.map(c => `<div class="comentario-item"><div class="comentario-autor">${c.autor}</div>${c.texto}</div>`).join("")}
        <div class="comentario-input-wrap">
          <input type="text" class="comentario-input" id="com-input-${p.id}" placeholder="Comentar...">
          <button class="btn-comentar" onclick="adicionarComentario(${p.id})">➤</button>
        </div>
      </div>
    `;
    lista.appendChild(div);
  });
}

// ===== CARREGAR DADOS =====
function carregarDados() {
  renderProdutos();
  renderTarefas();
  renderPosts();
  const notas = localStorage.getItem("notas");
  if (notas) document.getElementById("notas-texto").value = notas;
}

document.getElementById("barra-pesquisa").style.display = "flex";
fixHeight();
</script>
</body>
</html>
