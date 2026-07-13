<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title> Para ti</title>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@300;400;500;600;700&family=Fira+Code:wght@300;400;500;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #000;
    --red: #e05555;
    --red-dim: #e0555544;
    --red-faint: #e0555512;
    --text: #f0e8e8;
    --muted: #7a5050;
    --accent: #e07a55;
    --accent-dim: #e07a5544;
    --border: #2e1818;
    --surface: #0a0606;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'JetBrains Mono', monospace;
    min-height: 100vh;
    overflow-x: hidden;
  }

  #matrix-canvas {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    z-index: 0;
    opacity: 0.28;
    pointer-events: none;
  }

  .page {
    display: none;
    position: relative;
    z-index: 1;
    min-height: 100vh;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    padding: 40px 20px;
  }
  .page.active { display: flex; }

  .card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 48px 52px;
    max-width: 520px;
    width: 100%;
    position: relative;
    box-shadow: 0 0 60px #e0555508, inset 0 0 40px #00000060;
    text-align: center;
  }
  .card::before {
    content: '';
    position: absolute;
    inset: -1px;
    border-radius: 18px;
    background: linear-gradient(135deg, #e0555520, transparent 50%, #e07a5510);
    z-index: -1;
    pointer-events: none;
  }

  .card-corner {
    position: absolute;
    width: 10px; height: 10px;
    border-color: var(--red);
    border-style: solid;
    opacity: 0.3;
  }
  .card-corner.tl { top: 12px; left: 12px; border-width: 1px 0 0 1px; }
  .card-corner.tr { top: 12px; right: 12px; border-width: 1px 1px 0 0; }
  .card-corner.bl { bottom: 12px; left: 12px; border-width: 0 0 1px 1px; }
  .card-corner.br { bottom: 12px; right: 12px; border-width: 0 1px 1px 0; }

  h1 {
    font-size: 1.55rem;
    font-weight: 600;
    line-height: 1.4;
    color: var(--text);
    margin-bottom: 10px;
  }
  .heart {
    color: var(--red);
    display: inline-block;
    animation: pulse 1.8s ease-in-out infinite;
  }
  @keyframes pulse {
    0%,100% { transform: scale(1); }
    50% { transform: scale(1.3); }
  }

  .lock-icon {
    width: 52px; height: 52px;
    margin: 0 auto 28px;
    color: var(--muted);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .lock-icon svg { width: 100%; height: 100%; }

  .name-input-wrap {
    margin: 0 0 20px;
    position: relative;
  }
  .name-input {
    width: 100%;
    background: transparent;
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 14px 18px;
    font-family: 'JetBrains Mono', monospace;
    font-size: 1rem;
    color: var(--text);
    text-align: center;
    letter-spacing: 0.12em;
    outline: none;
    transition: border-color 0.2s, box-shadow 0.2s;
  }
  .name-input::placeholder { color: var(--muted); letter-spacing: 0.06em; font-size: 0.82rem; }
  .name-input:focus { border-color: var(--red); box-shadow: 0 0 18px var(--red-faint); }

  .error-msg {
    font-size: 0.75rem;
    color: #c04040;
    font-family: 'Fira Code', monospace;
    margin-top: 10px;
    min-height: 18px;
    opacity: 0;
    transition: opacity 0.25s;
  }
  .error-msg.show { opacity: 1; }

  .btn {
    display: inline-flex;
    align-items: center;
    gap: 10px;
    background: transparent;
    border: 1px solid var(--red);
    color: var(--red);
    font-family: 'JetBrains Mono', monospace;
    font-size: 0.9rem;
    font-weight: 500;
    padding: 14px 36px;
    border-radius: 10px;
    cursor: pointer;
    transition: all 0.25s ease;
    letter-spacing: 0.06em;
    position: relative;
    overflow: hidden;
  }
  .btn::before {
    content: '';
    position: absolute;
    inset: 0;
    background: var(--red-faint);
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 0.25s ease;
  }
  .btn:hover::before { transform: scaleX(1); }
  .btn:hover { box-shadow: 0 0 22px var(--red-dim); }
  .btn:disabled { border-color: var(--border); color: var(--muted); cursor: not-allowed; box-shadow: none; }
  .btn:disabled::before { display: none; }

  .btn-accent {
    border-color: rgba(255,255,255,0.55);
    color: #ffffff;
  }
  .btn-accent::before { background: rgba(255,255,255,0.06); }
  .btn-accent:hover { box-shadow: 0 0 22px rgba(255,255,255,0.15); border-color: #fff; }

  .big-text {
    font-size: 1.45rem;
    font-weight: 600;
    line-height: 1.5;
    margin-bottom: 6px;
  }
  .divider { height: 1px; background: var(--border); margin: 24px 0; }

  .invitation-box {
    background: var(--red-faint);
    border: 1px solid var(--red);
    border-radius: 12px;
    padding: 24px;
    margin: 20px 0;
    font-size: 0.95rem;
    line-height: 1.7;
    color: var(--text);
  }
  .invitation-box .emoji {
    font-size: 1.3rem;
    margin-right: 8px;
  }

  .confirm-options {
    display: flex;
    gap: 16px;
    justify-content: center;
    margin-top: 28px;
  }
  .confirm-btn {
    flex: 1;
    max-width: 160px;
  }
  .btn-yes {
    border-color: #4ade80;
    color: #4ade80;
  }
  .btn-yes::before { background: rgba(74, 222, 128, 0.1); }
  .btn-yes:hover { box-shadow: 0 0 22px rgba(74, 222, 128, 0.3); border-color: #4ade80; }

  .btn-no {
    border-color: #f87171;
    color: #f87171;
  }
  .btn-no::before { background: rgba(248, 113, 113, 0.1); }
  .btn-no:hover { box-shadow: 0 0 22px rgba(248, 113, 113, 0.3); border-color: #f87171; }

  .final-msg {
    font-size: 1.4rem;
    font-weight: 600;
    line-height: 1.55;
    color: var(--text);
    margin-bottom: 22px;
  }

  .gif-area {
    margin-top: 24px;
    width: 100%;
    border-radius: 12px;
    overflow: hidden;
    border: 1px solid var(--border);
  }
  .gif-area img { width: 100%; display: block; border-radius: 12px; }

  .scanlines {
    position: fixed; inset: 0; z-index: 0; pointer-events: none;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(224,85,85,0.012) 2px, rgba(224,85,85,0.012) 4px);
  }

  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(16px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .fade-in { animation: fadeUp 0.5s ease forwards; }

  @keyframes shake {
    0%,100% { transform: translateX(0); }
    20%     { transform: translateX(-6px); }
    40%     { transform: translateX(6px); }
    60%     { transform: translateX(-4px); }
    80%     { transform: translateX(4px); }
  }
  .shake { animation: shake 0.4s ease; }

  .footer {
    position: fixed;
    bottom: 14px;
    right: 18px;
    z-index: 100;
    font-family: 'JetBrains Mono', monospace;
    font-size: 12px;
    color: var(--muted);
    pointer-events: none;
  }
  .footer a {
    font-weight: 700;
    color: var(--muted);
    text-decoration: none;
    pointer-events: all;
    transition: color 0.2s;
  }
  .footer a:hover { color: var(--text); }
</style>
</head>
<body>

<canvas id="matrix-canvas"></canvas>
<div class="scanlines"></div>
<footer class="footer">by <a href="http://github.com/Sandreke/feliz-mes" target="_blank" rel="noopener">Sandreke</a></footer>

<!-- Page 1: name lock -->
<section class="page active fade-in" id="page-lock">
  <div class="card">
    <div class="card-corner tl"></div><div class="card-corner tr"></div>
    <div class="card-corner bl"></div><div class="card-corner br"></div>

    <div class="lock-icon">
      <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
        <rect x="3" y="11" width="18" height="11" rx="3" ry="3"/>
        <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
      </svg>
    </div>

    <h1>Esto se desbloquea<br>con tu nombre <span class="heart">❤</span></h1>
    <p class="sub" style="margin-top:8px; margin-bottom:24px;"></p>

    <div class="name-input-wrap">
      <input
        class="name-input"
        id="name-input"
        type="text"
        placeholder="escribe tu nombre..."
        autocomplete="off"
        onkeydown="if(event.key==='Enter') tryEnter()"
      />
      <div class="error-msg" id="error-msg">Este mensaje no es para ti.</div>
    </div>

    <button class="btn" id="enter-btn" onclick="tryEnter()">
      <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <path d="M15 3h4a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2h-4"/>
        <polyline points="10 17 15 12 10 7"/>
        <line x1="15" y1="12" x2="3" y2="12"/>
      </svg>
      Entrar
    </button>
  </div>
</section>

<!-- Page 2: invitation reveal -->
<section class="page" id="page-invitation">
  <div class="card fade-in">
    <div class="card-corner tl"></div><div class="card-corner tr"></div>
    <div class="card-corner bl"></div><div class="card-corner br"></div>

    <div class="big-text">Te estoy citando 🎊</div>
    <div class="divider"></div>

    <div class="invitation-box">
      <div><span class="emoji">📅</span><strong>Martes 14</strong></div>
      <div><span class="emoji">📍</span><strong>Seúl</strong></div>
      <div><span class="emoji">⏰</span><strong>8pm</strong></div>
      <div style="margin-top: 16px;"><span class="emoji">✨</span>Salida sorpresa, confía en mí</div>
    </div>

    <div class="divider"></div>

    <h1 style="font-size: 1.2rem; margin: 24px 0;">¿Confirmas? <span class="heart">💫</span></h1>

    <div class="confirm-options">
      <button class="btn btn-yes confirm-btn" onclick="respondYes()">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <polyline points="20 6 9 17 4 12"/>
        </svg>
        Sí
      </button>
      <button class="btn btn-no confirm-btn" onclick="respondNo()">
        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <line x1="18" y1="6" x2="6" y2="18"/>
          <line x1="6" y1="6" x2="18" y2="18"/>
        </svg>
        No
      </button>
    </div>
  </div>
</section>

<!-- Page 3: confirmed reveal -->
<section class="page" id="page-confirmed">
  <div class="card fade-in">
    <div class="card-corner tl"></div><div class="card-corner tr"></div>
    <div class="card-corner bl"></div><div class="card-corner br"></div>

    <div class="final-msg">
      ya eres hermoso,<br>
      estate listo para la sorpresa 🌟
    </div>

    <div class="gif-area">
      <img src="assets/garu-pucca.gif" alt="❤" />
    </div>
  </div>
</section>

<script>
const UNLOCK_NAME = 'andres'; // expected input, lowercase

// --- Binary Rain Background Animation ---
(function () {
  const canvas = document.getElementById('matrix-canvas');
  const ctx = canvas.getContext('2d');
  const chars = '01'; // Only binary digits
  let cols, drops;
  let speeds = []; // Variable speed for each column

  function init() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    cols = Math.floor(canvas.width / 20); // Wider spacing for binary
    drops = Array(cols).fill(1);
    speeds = Array(cols).fill(0).map(() => 0.5 + Math.random() * 1.5); // Random speeds
  }

  function draw() {
    // Trail effect - gradual fade
    ctx.fillStyle = 'rgba(0,0,0,0.08)';
    ctx.fillRect(0, 0, canvas.width, canvas.height);
    
    // Multiple colors for binary animation
    const colors = ['#e05555', '#e07a55', '#ff6b6b'];
    
    ctx.font = 'bold 18px JetBrains Mono, monospace';
    
    for (let i = 0; i < drops.length; i++) {
      // Random color for each character
      const color = colors[Math.floor(Math.random() * colors.length)];
      ctx.fillStyle = color;
      
      // Draw binary digit
      const char = chars[Math.floor(Math.random() * chars.length)];
      ctx.fillText(char, i * 20, drops[i] * 20);
      
      // Update drop position with variable speed
      if (drops[i] * 20 > canvas.height && Math.random() > 0.97) {
        drops[i] = 0;
        speeds[i] = 0.5 + Math.random() * 1.5; // New random speed
      }
      
      drops[i] += speeds[i] * 0.08; // Apply variable speed
    }
  }

  init();
  setInterval(draw, 50);
  window.addEventListener('resize', init);
})();

// --- Page transitions ---
function goTo(id) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  const target = document.getElementById(id);
  target.classList.add('active');
  target.style.animation = 'none';
  void target.offsetWidth;
  target.style.animation = 'fadeUp 0.5s ease forwards';
  window.scrollTo(0, 0);
}

// --- Name gate ---
function tryEnter() {
  const val = document.getElementById('name-input').value.trim().toLowerCase();
  const errEl = document.getElementById('error-msg');
  const card = document.querySelector('#page-lock .card');

  if (val === UNLOCK_NAME) {
    errEl.classList.remove('show');
    goTo('page-invitation');
  } else {
    errEl.classList.add('show');
    card.classList.remove('shake');
    void card.offsetWidth;
    card.classList.add('shake');
  }
}

// --- Confirmation ---
function respondYes() {
  goTo('page-confirmed');
}

function respondNo() {
  const card = document.querySelector('#page-invitation .card');
  card.classList.remove('shake');
  void card.offsetWidth;
  card.classList.add('shake');
  
  setTimeout(() => {
    alert('¡Anda, sé que dirás que sí! 😏');
  }, 400);
}
</script>
</body>
</html>
