<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
<title>Mentor Connect</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600;0,9..40,700&display=swap" rel="stylesheet"/>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2/dist/umd/supabase.js"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:216 50% 98%;--fg:222 47% 14%;
  --card:0 0% 100%;--primary:221 83% 53%;--primary-fg:210 40% 98%;
  --secondary:214 95% 93%;--secondary-fg:221 83% 40%;
  --muted:214 32% 95%;--muted-fg:215 16% 47%;
  --border:214 32% 91%;--destructive:0 84% 60%;--destructive-fg:210 40% 98%;
  --mc50:213 100% 97%;--mc100:214 95% 93%;--mc200:213 97% 87%;
  --mc300:212 96% 78%;--mc400:213 94% 68%;--mc500:217 91% 60%;
  --mc600:221 83% 53%;--mc700:224 76% 48%;--mc800:226 71% 40%;
  --mcg50:138 76% 97%;--mcg600:142 71% 45%;--mcg700:142 76% 36%;
  --shadow-sm:0 1px 3px hsl(221 83% 53%/.06);
  --shadow-md:0 4px 18px hsl(221 83% 53%/.10);
  --shadow-lg:0 8px 32px hsl(221 83% 53%/.14);
  --shadow-xl:0 16px 48px hsl(221 83% 53%/.18);
  --ease:cubic-bezier(.16,1,.3,1);
}
html,body{height:100%;overflow-x:hidden}
body{
  font-family:'DM Sans',system-ui,sans-serif;
  background:linear-gradient(135deg,hsl(var(--mc50)) 0%,hsl(var(--bg)) 50%,hsl(213 80% 94%) 100%);
  background-attachment:fixed;
  color:hsl(var(--fg));-webkit-font-smoothing:antialiased;
}
h1,h2,h3,h4,h5,h6{font-family:'DM Serif Display',Georgia,serif}
button{cursor:pointer;font-family:inherit;background:none;border:none;color:inherit}
input,textarea,select{font-family:inherit;background:hsl(var(--card));color:hsl(var(--fg));border:none;outline:none}
input::placeholder,textarea::placeholder{color:hsl(var(--mc600)/.3)}
svg{flex-shrink:0;display:inline-block}
a{text-decoration:none;color:inherit}

/* ── Loading ── */
#loading{position:fixed;inset:0;background:hsl(var(--bg));display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:9999;gap:1rem}
#loading.hidden{display:none}
.spinner{width:32px;height:32px;border:3px solid hsl(var(--mc200));border-top-color:hsl(var(--mc600));border-radius:50%;animation:spin .7s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}

/* ── Supabase banner ── */
#sb-banner{display:none;position:fixed;bottom:72px;left:0;right:0;z-index:200;margin:0 .75rem;padding:.625rem 1rem;border-radius:.75rem;background:hsl(var(--mcg50));border:1px solid hsl(142 76% 72%);color:hsl(var(--mcg700));font-size:.75rem;font-weight:500;text-align:center;pointer-events:none}
#sb-banner.error{background:hsl(var(--destructive)/.1);border-color:hsl(var(--destructive)/.4);color:hsl(var(--destructive))}
@media(min-width:768px){#sb-banner{bottom:1rem;max-width:400px;left:50%;transform:translateX(-50%)}}

/* ── Auth pages ── */
.auth-page{display:none;min-height:100vh;flex-direction:column;align-items:center;justify-content:center;padding:2rem 1rem}
.auth-page.active{display:flex}
.auth-wrap{width:100%;max-width:420px}

/* ── App Shell ── */
#shell{display:none;min-height:100vh}
#shell.active{display:block}

/* Sidebar */
.sidebar{
  display:none;position:fixed;left:0;top:0;bottom:0;width:224px;
  flex-direction:column;border-right:1px solid hsl(var(--border));z-index:50;
  background:rgba(248,250,252,.96);backdrop-filter:blur(20px);
}
@media(min-width:768px){.sidebar{display:flex}}
.sidebar-logo{display:flex;align-items:center;gap:.625rem;padding:1.25rem 1rem;border-bottom:1px solid hsl(var(--border))}
.sidebar-nav{display:flex;flex-direction:column;gap:.25rem;padding:.75rem;flex:1;overflow-y:auto}

/* Bottom nav */
.bottom-nav{
  position:fixed;bottom:0;left:0;right:0;height:64px;
  display:flex;align-items:stretch;
  border-top:1px solid hsl(var(--border));z-index:50;
  background:rgba(255,255,255,.97);backdrop-filter:blur(20px);
}
@media(min-width:768px){.bottom-nav{display:none}}
.bn-item{
  flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;
  gap:2px;font-size:9px;font-weight:500;color:hsl(var(--muted-fg));
  transition:color .15s;position:relative;
}
.bn-item.active{color:hsl(var(--primary))}

/* Nav items */
.nav-item{
  display:flex;align-items:center;gap:.75rem;padding:.625rem .875rem;
  border-radius:.75rem;font-size:.875rem;font-weight:500;cursor:pointer;
  color:hsl(var(--mc400));transition:all .15s;text-align:left;width:100%;
}
.nav-item:hover{color:hsl(var(--mc700));background:hsl(var(--mc50))}
.nav-item.active{color:hsl(var(--mc700));background:hsl(var(--mc50));font-weight:600}

/* Badge */
.badge-dot{position:absolute;top:5px;right:20%;min-width:14px;height:14px;border-radius:999px;background:hsl(var(--destructive));color:#fff;font-size:8px;font-weight:700;display:flex;align-items:center;justify-content:center;padding:0 2px}
.badge-nav{margin-left:auto;min-width:18px;height:18px;border-radius:999px;background:hsl(var(--destructive));color:#fff;font-size:11px;font-weight:700;display:inline-flex;align-items:center;justify-content:center;padding:0 4px}

/* Main content + swipeable pages */
.shell-main{overflow:hidden}
@media(min-width:768px){.shell-main{padding-left:224px}}
.pages-outer{overflow:hidden;width:100%;min-height:100vh;position:relative}
.pages-track{display:flex;will-change:transform;min-height:100vh}
.page-slot{min-width:100vw;width:100vw;min-height:100vh;overflow-y:auto;overflow-x:hidden;padding-bottom:72px}
@media(min-width:768px){
  .page-slot{min-width:calc(100vw - 224px);width:calc(100vw - 224px);padding-bottom:0}
  .pages-outer,.pages-track{min-height:100vh}
}
.pages-track.animating{transition:transform .3s var(--ease)}

/* Chat page (full screen, no swipe) */
#chat-overlay{display:none;position:fixed;inset:0;z-index:100;flex-direction:column;background:hsl(var(--bg))}
#chat-overlay.active{display:flex}

/* ── Cards ── */
.card{border-radius:1rem;border:1px solid rgba(255,255,255,.8);background:rgba(255,255,255,.92);backdrop-filter:blur(14px);box-shadow:var(--shadow-xl)}
.card-s{border-radius:1rem;border:1px solid rgba(255,255,255,.7);background:rgba(255,255,255,.92);backdrop-filter:blur(8px);box-shadow:var(--shadow-md);transition:box-shadow .2s,transform .2s var(--ease)}
.card-s:hover{box-shadow:var(--shadow-lg);transform:translateY(-2px)}

/* ── Avatar ── */
.av{border-radius:50%;display:inline-flex;align-items:center;justify-content:center;font-family:'DM Serif Display',serif;font-weight:600;flex-shrink:0;background:linear-gradient(135deg,hsl(var(--mc500)),hsl(var(--mc700)));color:#fff;box-shadow:0 4px 12px hsl(221 83% 53%/.25)}
.av-xs{width:32px;height:32px;font-size:.75rem}
.av-sm{width:40px;height:40px;font-size:.875rem}
.av-md{width:48px;height:48px;font-size:1rem}
.av-lg{width:60px;height:60px;font-size:1.25rem}

/* ── Badges ── */
.badge{display:inline-flex;align-items:center;gap:4px;padding:2px 10px;border-radius:999px;font-size:.75rem;font-weight:600}
.b-avail{background:hsl(var(--mcg50));color:hsl(var(--mcg700));border:1px solid hsl(142 76% 72%)}
.b-unavail{background:hsl(var(--muted));color:hsl(var(--muted-fg));border:1px solid hsl(var(--border))}
.b-role{background:hsl(var(--mc50));color:hsl(var(--mc700));border:1px solid hsl(var(--mc100));text-transform:capitalize}
.pill{display:inline-flex;align-items:center;gap:4px;padding:2px 8px;border-radius:999px;font-size:.75rem;background:hsl(var(--mc50));color:hsl(var(--mc600)/.75)}

/* ── Logo ── */
.logo-icon{width:36px;height:36px;border-radius:.75rem;display:inline-flex;align-items:center;justify-content:center;flex-shrink:0;background:linear-gradient(135deg,hsl(var(--mc600)),hsl(var(--mc800)));box-shadow:0 4px 16px hsl(221 83% 53%/.35)}
.logo-icon-lg{width:64px;height:64px;border-radius:1rem;display:inline-flex;align-items:center;justify-content:center;background:linear-gradient(135deg,hsl(var(--mc600)),hsl(var(--mc800)));box-shadow:0 8px 28px hsl(221 83% 53%/.38);margin:0 auto 1rem}

/* ── Inputs ── */
.inp{width:100%;padding:.625rem .875rem;border-radius:.75rem;font-size:.875rem;border:1.5px solid hsl(var(--mc200));background:hsl(var(--card));transition:border-color .2s,box-shadow .2s}
.inp:focus{border-color:hsl(var(--mc500));box-shadow:0 0 0 3px hsl(221 83% 53%/.11)}

/* ── Buttons ── */
.btn{display:inline-flex;align-items:center;justify-content:center;gap:.5rem;padding:.75rem 1.25rem;border-radius:.75rem;font-weight:600;font-size:.875rem;transition:transform .15s,opacity .15s;cursor:pointer;width:100%}
.btn:active{transform:scale(.98)}
.btn:disabled{opacity:.4;pointer-events:none}
.btn-p{background:hsl(var(--primary));color:#fff;box-shadow:0 4px 18px hsl(221 83% 53%/.35)}
.btn-p:hover{transform:translateY(-1px)}
.btn-o{border:2px solid hsl(var(--mc200));color:hsl(var(--mc700))}
.btn-o:hover{background:hsl(var(--mc50));border-color:hsl(var(--mc400))}
.btn-d{border:2px solid hsl(var(--destructive)/.3);color:hsl(var(--destructive))}
.btn-d:hover{background:hsl(var(--destructive)/.05)}
.btn-sm{padding:.5rem .875rem;font-size:.75rem;width:auto}

/* ── Top bar ── */
.topbar{position:sticky;top:0;z-index:40;border-bottom:1px solid hsl(var(--mc100));background:rgba(255,255,255,.88);backdrop-filter:blur(16px)}

/* ── Form utils ── */
.err{background:hsl(var(--destructive)/.1);border:1px solid hsl(var(--destructive)/.2);color:hsl(var(--destructive));border-radius:.75rem;padding:.75rem 1rem;font-size:.875rem;margin-bottom:.75rem}
.succ{display:flex;align-items:center;gap:.5rem;background:hsl(var(--mcg50));border:1px solid hsl(142 76% 72%);color:hsl(var(--mcg700));border-radius:.75rem;padding:.75rem 1rem;font-size:.875rem;animation:slideIn .25s ease both}

/* ── Autocomplete ── */
.ac-drop{position:absolute;top:100%;left:0;right:0;margin-top:4px;max-height:10rem;overflow-y:auto;border-radius:.75rem;border:1px solid hsl(var(--mc200));background:hsl(var(--card));box-shadow:var(--shadow-lg);z-index:200}
.ac-opt{width:100%;text-align:left;padding:.5rem .75rem;font-size:.875rem;background:none;cursor:pointer;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;border:none;display:block}
.ac-opt:hover{background:hsl(var(--mc50))}

/* ── Toggle ── */
.toggle{position:relative;display:inline-block;width:44px;height:24px;cursor:pointer}
.toggle input{position:absolute;opacity:0;width:0;height:0}
.toggle-sl{position:absolute;inset:0;border-radius:999px;background:hsl(215 20% 78%);transition:background .2s}
.toggle-sl::before{content:'';position:absolute;width:18px;height:18px;border-radius:50%;background:#fff;left:3px;bottom:3px;transition:transform .2s;box-shadow:0 1px 4px rgba(0,0,0,.18)}
.toggle input:checked+.toggle-sl{background:hsl(var(--mc600))}
.toggle input:checked+.toggle-sl::before{transform:translateX(20px)}

/* ── Satisfaction bar ── */
.sat-bar{height:8px;border-radius:999px;overflow:hidden;background:hsl(var(--mc100))}
.sat-fill{height:100%;border-radius:999px;background:linear-gradient(90deg,hsl(var(--mc500)),hsl(var(--mc600)));transition:width .7s var(--ease)}

/* ── Review card ── */
.rev-card{border-radius:1rem;padding:1.25rem;border:1px solid rgba(255,255,255,.7);background:rgba(255,255,255,.92);backdrop-filter:blur(8px);box-shadow:var(--shadow-md)}
.rev-card.featured{box-shadow:0 0 0 2px hsl(45 100% 70%/.5),var(--shadow-md)}

/* ── Grid ── */
.g2{display:grid;gap:1rem;grid-template-columns:1fr}
@media(min-width:640px){.g2{grid-template-columns:1fr 1fr}}
.g3{display:grid;gap:1rem;grid-template-columns:repeat(auto-fill,minmax(260px,1fr))}

/* ── Resource type colors ── */
.cat-debate{background:hsl(var(--mc100));color:hsl(var(--mc700))}
.cat-public_speaking{background:#f3e8ff;color:#6b21a8}
.cat-coaching{background:#d1fae5;color:#065f46}
.cat-judging{background:#ffedd5;color:#9a3412}
.cat-general{background:hsl(var(--muted));color:hsl(var(--muted-fg))}

/* ── Person card ── */
.person-card{position:relative;overflow:hidden}
.pcard-line{position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,hsl(var(--mc400)),hsl(var(--mc600)));opacity:0;transition:opacity .2s}
.person-card:hover .pcard-line{opacity:1}

/* ── Animations ── */
@keyframes reveal{to{opacity:1;transform:translateY(0)}}
@keyframes slideIn{from{opacity:0;transform:scale(.97) translateY(8px)}to{opacity:1;transform:scale(1) translateY(0)}}
@keyframes fadeIn{from{opacity:0;transform:translateY(6px)}to{opacity:1;transform:translateY(0)}}
.rev{opacity:0;transform:translateY(16px);animation:reveal .6s var(--ease) forwards}
.rev-1{animation-delay:80ms}.rev-2{animation-delay:160ms}.rev-3{animation-delay:240ms}

/* ── Modal overlay ── */
.modal-bg{position:fixed;inset:0;background:rgba(0,0,0,.45);z-index:500;display:flex;align-items:center;justify-content:center;padding:1rem}
.modal{background:hsl(var(--card));border-radius:1.25rem;padding:1.5rem;width:100%;max-width:440px;box-shadow:var(--shadow-xl);animation:slideIn .25s ease both}

/* ── Scrollbar ── */
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:hsl(var(--mc200));border-radius:3px}

/* ── Chat messages ── */
.bubble-me{background:hsl(var(--primary));color:#fff;border-radius:1rem 1rem .25rem 1rem}
.bubble-them{background:hsl(var(--card));color:hsl(var(--fg));border-radius:1rem 1rem 1rem .25rem;box-shadow:var(--shadow-sm)}

/* ── Misc ── */
.sep{border:none;border-top:1px solid hsl(var(--mc50));margin:.75rem 0}
.clamp2{display:-webkit-box;-webkit-line-clamp:2;-webkit-box-orient:vertical;overflow:hidden}
.truncate{overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
</style>
</head>
<body>

<!-- Loading -->
<div id="loading">
  <div style="width:56px;height:56px;border-radius:1rem;display:flex;align-items:center;justify-content:center;background:linear-gradient(135deg,hsl(221 83% 53%),hsl(226 71% 40%));box-shadow:0 8px 28px hsl(221 83% 53%/.38)">
    <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9.937 15.5A2 2 0 0 0 8.5 14.063l-6.135-1.582a.5.5 0 0 1 0-.962L8.5 9.936A2 2 0 0 0 9.937 8.5l1.582-6.135a.5.5 0 0 1 .963 0L14.063 8.5A2 2 0 0 0 15.5 9.937l6.135 1.581a.5.5 0 0 1 0 .964L15.5 14.063a2 2 0 0 0-1.437 1.437l-1.582 6.135a.5.5 0 0 1-.963 0z"/><path d="M20 3v4"/><path d="M22 5h-4"/><path d="M4 17v2"/><path d="M5 18H3"/></svg>
  </div>
  <div class="spinner"></div>
  <p style="font-size:.875rem;color:hsl(var(--muted-fg))">Loading Mentor Connect…</p>
</div>

<!-- Supabase status banner -->
<div id="sb-banner"></div>

<!-- ═══════════════ AUTH PAGES ═══════════════ -->

<!-- Landing -->
<div class="auth-page" id="pg-landing">
  <div class="auth-wrap">
    <div style="text-align:center;margin-bottom:1.75rem" class="rev">
      <div class="logo-icon-lg" id="landing-logo"></div>
      <h1 style="font-size:2.25rem;letter-spacing:-.02em">Mentor Connect</h1>
      <p style="color:hsl(var(--muted-fg));font-size:.875rem;margin-top:.25rem">The mentoring community hub</p>
    </div>
    <div class="card" style="padding:1.5rem" id="landing-card">
      <div style="display:flex;flex-direction:column;gap:.75rem">
        <button class="btn btn-p" onclick="showAuth('login')"><span id="ic-login"></span> Log In</button>
        <button class="btn btn-o" onclick="showAuth('signup')"><span id="ic-useradd"></span> Create Account</button>
      </div>
    </div>
    <div id="role-tiles" style="display:grid;grid-template-columns:1fr 1fr;gap:.75rem;margin-top:1.5rem"></div>
    <div id="landing-reviews" style="margin-top:2rem"></div>
    <p style="text-align:center;font-size:.75rem;color:hsl(var(--muted-fg));margin-top:1.5rem">
      Demo: any email below + password <code style="background:hsl(var(--mc50));padding:2px 6px;border-radius:4px;color:hsl(var(--mc700))">demo123</code>
    </p>
  </div>
</div>

<!-- Login -->
<div class="auth-page" id="pg-login">
  <div class="auth-wrap">
    <div style="text-align:center;margin-bottom:1.75rem" class="rev">
      <div class="logo-icon-lg" id="login-logo"></div>
      <h1 style="font-size:2.25rem;letter-spacing:-.02em">Mentor Connect</h1>
    </div>
    <div class="card rev rev-1">
      <div style="padding:1.5rem 1.5rem 0">
        <button onclick="showAuth('landing')" style="display:flex;align-items:center;gap:4px;color:hsl(var(--mc400));font-size:.875rem;margin-bottom:.75rem" id="login-back-btn"></button>
        <h2 style="font-size:1.5rem">Welcome back</h2>
        <p style="color:hsl(var(--muted-fg));font-size:.875rem;margin-top:.25rem">Sign in to your account</p>
      </div>
      <div style="padding:1.5rem">
        <div id="login-err"></div>
        <div style="display:flex;flex-direction:column;gap:.75rem">
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Email</label>
            <input type="email" id="li-email" class="inp" placeholder="you@example.com" onkeydown="if(event.key==='Enter')doLogin()"/></div>
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Password</label>
            <div style="position:relative">
              <input type="password" id="li-pw" class="inp" style="padding-right:2.5rem" placeholder="••••••••" onkeydown="if(event.key==='Enter')doLogin()"/>
              <button onclick="toggleEye('li-pw','li-eye')" style="position:absolute;right:.75rem;top:50%;transform:translateY(-50%)" id="li-eye"></button>
            </div></div>
          <button class="btn btn-p" onclick="doLogin()">Log In</button>
          <p style="text-align:center;font-size:.875rem;color:hsl(var(--muted-fg));margin-top:.25rem">
            Don't have an account? <button onclick="showAuth('signup')" style="color:hsl(var(--primary));font-weight:600">Sign up</button>
          </p>
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Signup -->
<div class="auth-page" id="pg-signup">
  <div class="auth-wrap">
    <div style="text-align:center;margin-bottom:1.75rem" class="rev">
      <div class="logo-icon-lg" id="signup-logo"></div>
      <h1 style="font-size:2.25rem;letter-spacing:-.02em">Mentor Connect</h1>
    </div>
    <div class="card rev rev-1" id="su-card">
      <div style="padding:1.5rem 1.5rem 0">
        <button onclick="suBack()" style="display:flex;align-items:center;gap:4px;color:hsl(var(--muted-fg));font-size:.875rem;margin-bottom:.75rem" id="su-back-btn"></button>
        <h2 style="font-size:1.5rem">Create an account</h2>
        <p style="color:hsl(var(--muted-fg));font-size:.875rem;margin-top:.25rem" id="su-step-lbl">Step 1 of 2 — Choose your role</p>
      </div>
      <div style="padding:1.5rem" id="su-body"></div>
    </div>
  </div>
</div>

<!-- ═══════════════ APP SHELL ═══════════════ -->
<div id="shell">
  <!-- Desktop Sidebar -->
  <nav class="sidebar" id="sidebar">
    <div class="sidebar-logo">
      <div class="logo-icon" id="sb-logo"></div>
      <span style="font-family:'DM Serif Display',serif;font-size:1rem">Mentor Connect</span>
    </div>
    <div class="sidebar-nav" id="sb-nav"></div>
    <div style="padding:1rem;border-top:1px solid hsl(var(--border))">
      <button onclick="doLogout()" style="display:flex;align-items:center;gap:.5rem;font-size:.8125rem;color:hsl(var(--muted-fg));padding:.5rem .75rem;border-radius:.75rem;width:100%;transition:background .15s" onmouseover="this.style.background='hsl(var(--muted))'" onmouseout="this.style.background=''">
        <span id="sb-logout-ic"></span> Log Out
      </button>
    </div>
  </nav>

  <!-- Main -->
  <div class="shell-main">
    <div class="pages-outer" id="pages-outer">
      <div class="pages-track" id="pages-track">
        <div class="page-slot" id="slot-mentors"></div>
        <div class="page-slot" id="slot-judges"></div>
        <div class="page-slot" id="slot-messages"></div>
        <div class="page-slot" id="slot-notifications"></div>
        <div class="page-slot" id="slot-resources"></div>
        <div class="page-slot" id="slot-reviews"></div>
        <div class="page-slot" id="slot-settings"></div>
      </div>
    </div>
  </div>

  <!-- Bottom Nav -->
  <nav class="bottom-nav" id="bottom-nav"></nav>
</div>

<!-- Chat Overlay (full screen, above swipe) -->
<div id="chat-overlay">
  <div class="topbar">
    <div style="padding:.75rem 1rem;display:flex;align-items:center;gap:.75rem">
      <button onclick="closeChat()" id="chat-back-btn"></button>
      <div class="av av-sm" id="chat-av"></div>
      <div><h2 style="font-weight:600;font-size:.875rem" id="chat-name"></h2></div>
    </div>
  </div>
  <div id="chat-msgs" style="flex:1;overflow-y:auto;padding:1rem;display:flex;flex-direction:column;gap:.5rem"></div>
  <div style="border-top:1px solid hsl(var(--mc100));padding:.75rem 1rem;display:flex;gap:.625rem;align-items:flex-end;background:rgba(255,255,255,.92);backdrop-filter:blur(14px)">
    <textarea id="chat-inp" class="inp" style="resize:none;max-height:6rem;flex:1" rows="1" placeholder="Type a message…" onkeydown="if(event.key==='Enter'&&!event.shiftKey){event.preventDefault();sendMsg()}"></textarea>
    <button onclick="sendMsg()" style="width:40px;height:40px;border-radius:.75rem;background:hsl(var(--primary));color:#fff;display:flex;align-items:center;justify-content:center;flex-shrink:0" id="chat-send-btn"></button>
  </div>
</div>

<script>
'use strict';
// ═══════════════════════════════════════════
// ICONS — inline SVG helper
// ═══════════════════════════════════════════
const SVGD = {
  sparkles:`<path d="M9.937 15.5A2 2 0 0 0 8.5 14.063l-6.135-1.582a.5.5 0 0 1 0-.962L8.5 9.936A2 2 0 0 0 9.937 8.5l1.582-6.135a.5.5 0 0 1 .963 0L14.063 8.5A2 2 0 0 0 15.5 9.937l6.135 1.581a.5.5 0 0 1 0 .964L15.5 14.063a2 2 0 0 0-1.437 1.437l-1.582 6.135a.5.5 0 0 1-.963 0z"/><path d="M20 3v4"/><path d="M22 5h-4"/><path d="M4 17v2"/><path d="M5 18H3"/>`,
  'log-in':`<path d="M15 3h4a2 2 0 0 1 2 2v14a2 2 0 0 1-2 2h-4"/><polyline points="10 17 15 12 10 7"/><line x1="15" x2="3" y1="12" y2="12"/>`,
  'user-plus':`<path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><line x1="19" x2="19" y1="8" y2="14"/><line x1="22" x2="16" y1="11" y2="11"/>`,
  users:`<path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><path d="M22 21v-2a4 4 0 0 0-3-3.87"/><path d="M16 3.13a4 4 0 0 1 0 7.75"/>`,
  scale:`<path d="m16 16 3-8 3 8c-.87.65-1.92 1-3 1s-2.13-.35-3-1Z"/><path d="m2 16 3-8 3 8c-.87.65-1.92 1-3 1s-2.13-.35-3-1Z"/><path d="M7 21H17"/><path d="M12 3v18"/><path d="M3 7h2c2 0 5-1 7-2 2 1 5 2 7 2h2"/>`,
  'book-open':`<path d="M2 3h6a4 4 0 0 1 4 4v14a3 3 0 0 0-3-3H2z"/><path d="M22 3h-6a4 4 0 0 0-4 4v14a3 3 0 0 1 3-3h7z"/>`,
  'graduation-cap':`<path d="M21.42 10.922a1 1 0 0 0-.019-1.838L12.83 5.18a2 2 0 0 0-1.66 0L2.6 9.08a1 1 0 0 0 0 1.832l8.57 3.908a2 2 0 0 0 1.66 0z"/><path d="M22 10v6"/><path d="M6 12.5V16a6 3 0 0 0 12 0v-3.5"/>`,
  star:`<polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/>`,
  'chevron-left':`<path d="m15 18-6-6 6-6"/>`,
  'chevron-right':`<path d="m9 18 6-6-6-6"/>`,
  'chevron-down':`<path d="m6 9 6 6 6-6"/>`,
  eye:`<path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z"/><circle cx="12" cy="12" r="3"/>`,
  'eye-off':`<path d="M9.88 9.88a3 3 0 1 0 4.24 4.24"/><path d="M10.73 5.08A10.43 10.43 0 0 1 12 5c7 0 10 7 10 7a13.16 13.16 0 0 1-1.67 2.68"/><path d="M6.61 6.61A13.526 13.526 0 0 0 2 12s3 7 10 7a9.74 9.74 0 0 0 5.39-1.61"/><line x1="2" x2="22" y1="2" y2="22"/>`,
  check:`<path d="M20 6 9 17l-5-5"/>`,
  x:`<path d="M18 6 6 18"/><path d="m6 6 12 12"/>`,
  plus:`<path d="M5 12h14"/><path d="M12 5v14"/>`,
  search:`<circle cx="11" cy="11" r="8"/><path d="m21 21-4.3-4.3"/>`,
  'map-pin':`<path d="M20 10c0 6-8 12-8 12s-8-6-8-12a8 8 0 0 1 16 0Z"/><circle cx="12" cy="10" r="3"/>`,
  'message-circle':`<path d="m3 21 1.9-5.7a8.5 8.5 0 1 1 3.8 3.8z"/>`,
  bell:`<path d="M6 8a6 6 0 0 1 12 0c0 7 3 9 3 9H3s3-2 3-9"/><path d="M10.3 21a1.94 1.94 0 0 0 3.4 0"/>`,
  settings:`<path d="M12.22 2h-.44a2 2 0 0 0-2 2v.18a2 2 0 0 1-1 1.73l-.43.25a2 2 0 0 1-2 0l-.15-.08a2 2 0 0 0-2.73.73l-.22.38a2 2 0 0 0 .73 2.73l.15.1a2 2 0 0 1 1 1.72v.51a2 2 0 0 1-1 1.74l-.15.09a2 2 0 0 0-.73 2.73l.22.38a2 2 0 0 0 2.73.73l.15-.08a2 2 0 0 1 2 0l.43.25a2 2 0 0 1 1 1.73V20a2 2 0 0 0 2 2h.44a2 2 0 0 0 2-2v-.18a2 2 0 0 1 1-1.73l.43-.25a2 2 0 0 1 2 0l.15.08a2 2 0 0 0 2.73-.73l.22-.39a2 2 0 0 0-.73-2.73l-.15-.08a2 2 0 0 1-1-1.74v-.5a2 2 0 0 1 1-1.74l.15-.09a2 2 0 0 0 .73-2.73l-.22-.38a2 2 0 0 0-2.73-.73l-.15.08a2 2 0 0 1-2 0l-.43-.25a2 2 0 0 1-1-1.73V4a2 2 0 0 0-2-2z"/><circle cx="12" cy="12" r="3"/>`,
  send:`<path d="m22 2-7 20-4-9-9-4 20-7z"/><path d="M22 2 11 13"/>`,
  user:`<path d="M19 21v-2a4 4 0 0 0-4-4H9a4 4 0 0 0-4 4v2"/><circle cx="12" cy="7" r="4"/>`,
  shield:`<path d="M20 13c0 5-3.5 7.5-7.66 8.95a1 1 0 0 1-.67-.01C7.5 20.5 4 18 4 13V6a1 1 0 0 1 1-1c2 0 4.5-1.2 6.24-2.72a1.17 1.17 0 0 1 1.52 0C14.51 3.81 17 5 19 5a1 1 0 0 1 1 1z"/>`,
  'log-out':`<path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" x2="9" y1="12" y2="12"/>`,
  'trash-2':`<path d="M3 6h18"/><path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6"/><path d="M8 6V4c0-1 1-2 2-2h4c1 0 2 1 2 2v2"/><line x1="10" x2="10" y1="11" y2="17"/><line x1="14" x2="14" y1="11" y2="17"/>`,
  save:`<path d="M15.2 3a2 2 0 0 1 1.4.6l3.8 3.8a2 2 0 0 1 .6 1.4V19a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2z"/><path d="M17 21v-7a1 1 0 0 0-1-1H8a1 1 0 0 0-1 1v7"/><path d="M7 3v4a1 1 0 0 0 1 1h7"/>`,
  mail:`<rect width="20" height="16" x="2" y="4" rx="2"/><path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7"/>`,
  'thumbs-up':`<path d="M7 10v12"/><path d="M15 5.88 14 10h5.83a2 2 0 0 1 1.92 2.56l-2.33 8A2 2 0 0 1 17.5 22H4a2 2 0 0 1-2-2v-8a2 2 0 0 1 2-2h2.76a2 2 0 0 0 1.79-1.11L12 2a3.13 3.13 0 0 1 3 3.88Z"/>`,
  quote:`<path d="M3 21c3 0 7-1 7-8V5c0-1.25-.756-2.017-2-2H4c-1.25 0-2 .75-2 1.972V11c0 1.25.75 2 2 2 1 0 1 0 1 1v1c0 1-1 2-2 2s-1 .008-1 1.031V20c0 1 0 1 1 1z"/><path d="M15 21c3 0 7-1 7-8V5c0-1.25-.757-2.017-2-2h-4c-1.25 0-2 .75-2 1.972V11c0 1.25.75 2 2 2h.75c0 2.25.25 4-2.75 4v3c0 1 0 1 1 1z"/>`,
  crown:`<path d="M11.562 3.266a.5.5 0 0 1 .876 0L15.39 8.87a1 1 0 0 0 1.516.294L21.183 5.5a.5.5 0 0 1 .798.519l-2.834 10.246a1 1 0 0 1-.956.734H5.81a1 1 0 0 1-.957-.734L2.02 6.02a.5.5 0 0 1 .798-.519l4.276 3.664a1 1 0 0 0 1.516-.294z"/><path d="M5 21h14"/>`,
  pencil:`<path d="M21.174 6.812a1 1 0 0 0-3.986-3.987L3.842 16.174a2 2 0 0 0-.5.83l-1.321 4.352a.5.5 0 0 0 .623.622l4.353-1.32a2 2 0 0 0 .83-.497z"/>`,
  'message-square-plus':`<path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z"/><path d="M12 7v6"/><path d="M9 10h6"/>`,
  video:`<path d="m16 13 5.223 3.482a.5.5 0 0 0 .777-.416V7.87a.5.5 0 0 0-.752-.432L16 10.5"/><rect x="2" y="6" width="14" height="12" rx="2"/>`,
  'file-text':`<path d="M15 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V7Z"/><path d="M14 2v4a2 2 0 0 0 2 2h4"/><path d="M10 9H8"/><path d="M16 13H8"/><path d="M16 17H8"/>`,
  'link-2':`<path d="M9 17H7A5 5 0 0 1 7 7h2"/><path d="M15 7h2a5 5 0 1 1 0 10h-2"/><line x1="8" x2="16" y1="12" y2="12"/>`,
  'external-link':`<path d="M15 3h6v6"/><path d="M10 14 21 3"/><path d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6"/>`,
  'arrow-left':`<path d="m12 19-7-7 7-7"/><path d="M19 12H5"/>`,
};
function ic(name, size=16, color='currentColor', sw=2) {
  return `<svg width="${size}" height="${size}" viewBox="0 0 24 24" fill="none" stroke="${color}" stroke-width="${sw}" stroke-linecap="round" stroke-linejoin="round" style="flex-shrink:0">${SVGD[name]||''}</svg>`;
}
function setIc(id, name, size=16, color='currentColor') {
  const el = document.getElementById(id);
  if(el) el.innerHTML = ic(name, size, color);
}

// ═══════════════════════════════════════════
// SUPABASE
// ═══════════════════════════════════════════
const SB_URL = 'https://knkclotptwudbqdwjqxp.supabase.co';
const SB_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6Imtua2Nsb3RwdHd1ZGJxZHdqcXhwIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NzIyOTQ1NjUsImV4cCI6MjA4Nzg3MDU2NX0.dk9OlP3DMP_Rw5cvxvlUYGEuEngnerdpNXu9iNI9ibc';
let sb = null;
let sbOk = false;

try { sb = window.supabase.createClient(SB_URL, SB_KEY); } catch(e) { console.warn('Supabase init failed', e); }

function showBanner(msg, isErr=false) {
  const el = document.getElementById('sb-banner');
  if(!el) return;
  el.textContent = msg;
  el.className = isErr ? 'error' : '';
  el.style.display = 'block';
  setTimeout(() => { el.style.display = 'none'; }, 4000);
}

// ═══════════════════════════════════════════
// DATA — DEMO DEFAULTS
// ═══════════════════════════════════════════
const DEMO_USERS = [
  {id:'u1',email:'sarah.chen@example.com',password:'demo123',first_name:'Sarah',last_name:'Chen',role:'mentor',location:'California',school:'Stanford University',description:'Former national debate champion. Passionate about helping students develop critical thinking skills through Lincoln-Douglas debate.',available_for_hire:true,email_verified:true,tabroom_linked:false},
  {id:'u2',email:'marcus.j@example.com',password:'demo123',first_name:'Marcus',last_name:'Johnson',role:'mentor',location:'New York',school:'Columbia University',description:'Policy debate coach with 8 years of experience. Specializing in evidence research and case construction.',available_for_hire:true,email_verified:true,tabroom_linked:false},
  {id:'u3',email:'elena.r@example.com',password:'demo123',first_name:'Elena',last_name:'Rodriguez',role:'judge',location:'Texas',description:'Certified judge with experience in state and national tournaments.',available_for_hire:true,tabroom_username:'elenajudge',tabroom_linked:true,email_verified:true},
  {id:'u4',email:'david.kim@example.com',password:'demo123',first_name:'David',last_name:'Kim',role:'judge',location:'Illinois',school:'University of Chicago',description:'Philosophy background, experienced in LD and parliamentary debate judging.',available_for_hire:true,tabroom_username:'dkim_judge',tabroom_linked:true,email_verified:true},
  {id:'u5',email:'anna.w@example.com',password:'demo123',first_name:'Anna',last_name:'Williams',role:'mentor',location:'Massachusetts',school:'Harvard University',description:'Public forum debate specialist.',available_for_hire:false,email_verified:true,tabroom_linked:false},
  {id:'u6',email:'coach.davis@example.com',password:'demo123',first_name:'Robert',last_name:'Davis',role:'coach',location:'Georgia',school:'Emory University',description:'Head debate coach. Building competitive teams for over a decade.',available_for_hire:false,email_verified:true,tabroom_linked:false},
  {id:'u7',email:'priya.s@example.com',password:'demo123',first_name:'Priya',last_name:'Sharma',role:'mentor',location:'Washington',school:'University of Washington',description:'Congressional debate mentor.',available_for_hire:true,email_verified:true,tabroom_linked:false},
  {id:'u8',email:'james.l@example.com',password:'demo123',first_name:'James',last_name:'Liu',role:'judge',location:'California',description:'Tournament organizer and judge. 15+ years in the debate community.',available_for_hire:true,tabroom_linked:false,email_verified:true},
];
const DEMO_RESOURCES = [
  {id:'r1',title:'Introduction to Lincoln-Douglas Debate',type:'document',category:'debate',description:'A comprehensive guide to LD debate format, including value/criterion framework construction.',url:'#',posted_by_name:'Mentor Connect Team',created_date:'2024-11-15T00:00:00Z'},
  {id:'r2',title:'How to Research Evidence Effectively',type:'video',category:'debate',description:'Learn how to find and cut cards efficiently for policy debate.',url:'#',posted_by_name:'Mentor Connect Team',created_date:'2024-12-01T00:00:00Z'},
  {id:'r3',title:'Public Speaking Fundamentals',type:'link',category:'public_speaking',description:'Tips for improving your delivery, eye contact, and vocal variety.',url:'#',posted_by_name:'Mentor Connect Team',created_date:'2025-01-10T00:00:00Z'},
  {id:'r4',title:'Judging Philosophy Examples',type:'document',category:'judging',description:'Sample judging philosophies from experienced coaches and judges.',url:'#',posted_by_name:'Mentor Connect Team',created_date:'2025-02-01T00:00:00Z'},
];
const DEMO_REVIEWS = [
  {id:'rv1',user_name:'Mia Tran',user_role:'student',rating:5,text:'Found an amazing LD mentor in under a day. My speaker points went up 2 points at the next tournament!',date:'2025-02-14',satisfaction_pct:98,front_page:true},
  {id:'rv2',user_name:'Jordan Bell',user_role:'coach',rating:5,text:'Recruited 3 qualified judges for our invitational through Mentor Connect. Saved us weeks of searching.',date:'2025-01-28',satisfaction_pct:95,front_page:true},
  {id:'rv3',user_name:'Aisha Patel',user_role:'student',rating:4,text:'The resources section is a goldmine. Finally understood the value/criterion framework.',date:'2025-03-02',satisfaction_pct:91,front_page:true},
];

const US_STATES=['Alabama','Alaska','Arizona','Arkansas','California','Colorado','Connecticut','Delaware','Florida','Georgia','Hawaii','Idaho','Illinois','Indiana','Iowa','Kansas','Kentucky','Louisiana','Maine','Maryland','Massachusetts','Michigan','Minnesota','Mississippi','Missouri','Montana','Nebraska','Nevada','New Hampshire','New Jersey','New Mexico','New York','North Carolina','North Dakota','Ohio','Oklahoma','Oregon','Pennsylvania','Rhode Island','South Carolina','South Dakota','Tennessee','Texas','Utah','Vermont','Virginia','Washington','West Virginia','Wisconsin','Wyoming'];
const COUNTRIES=['United States','United Kingdom','Canada','Australia','Germany','France','India','Japan','South Korea','Brazil','Mexico','China','Netherlands','Sweden','Norway','Denmark','Finland','Switzerland','Spain','Italy','Portugal','Poland','Turkey','Israel','South Africa','Nigeria','Kenya','Argentina','Chile','Indonesia','Malaysia','Philippines','Thailand','New Zealand','Ireland'];
const SCHOOLS=['Harvard University','Stanford University','MIT','Yale University','Princeton University','Columbia University','University of Chicago','University of Pennsylvania','Johns Hopkins University','Duke University','Northwestern University','Vanderbilt University','Rice University','Dartmouth College','Notre Dame','Georgetown University','Carnegie Mellon University','UC Berkeley','UCLA','University of Virginia','University of Michigan','University of Washington','Emory University','University of Southern California','New York University','Boston University','Tufts University','Boston College'];

// ═══════════════════════════════════════════
// STATE
// ═══════════════════════════════════════════
const LS = {
  get(k,fb){try{const v=localStorage.getItem(k);return v?JSON.parse(v):fb}catch{return fb}},
  set(k,v){try{localStorage.setItem(k,JSON.stringify(v))}catch{}},
};

const ST = {
  currentUser: LS.get('mc_user', null),
  users: LS.get('mc_users', DEMO_USERS),
  conversations: LS.get('mc_convos', []),
  messages: LS.get('mc_msgs', {}),
  notifications: LS.get('mc_notifs', []),
  resources: LS.get('mc_res', DEMO_RESOURCES),
  reviews: LS.get('mc_revs', DEMO_REVIEWS),
  // UI
  page: 'mentors',
  pageIdx: 0,
  convoId: null,
  mSearch:'', mLoc:'',
  jSearch:'', jLoc:'',
  resCat:'',
  selRes: null,
  showResForm: false,
  showRevForm: false,
  editingRev: null,
  revRating: 5,
  revText: '',
  revSat: 95,
  expandedAdmin: null,
};
function isAdmin(u){ return u && (u.role==='admin' || u.email==='ethav31@gmail.com'); }
function save() {
  LS.set('mc_user', ST.currentUser);
  LS.set('mc_users', ST.users);
  LS.set('mc_convos', ST.conversations);
  LS.set('mc_msgs', ST.messages);
  LS.set('mc_notifs', ST.notifications);
  LS.set('mc_res', ST.resources);
  LS.set('mc_revs', ST.reviews);
}

// ═══════════════════════════════════════════
// SUPABASE SYNC
// ═══════════════════════════════════════════
async function sbLoad() {
  if(!sb) return false;
  try {
    const [pu, pr, rv] = await Promise.all([
      sb.from('profiles').select('*'),
      sb.from('resources').select('*'),
      sb.from('reviews').select('*'),
    ]);
    if(pu.error) throw pu.error;
    if(pu.data && pu.data.length > 0) {
      ST.users = pu.data;
      LS.set('mc_users', ST.users);
    } else if(pu.data && pu.data.length === 0) {
      // Seed demo users
      for(const u of DEMO_USERS) {
        await sb.from('profiles').upsert(u, {onConflict:'id'});
      }
    }
    if(pr.data && pr.data.length > 0) { ST.resources = pr.data; LS.set('mc_res', ST.resources); }
    else if(pr.data && pr.data.length === 0) {
      for(const r of DEMO_RESOURCES) await sb.from('resources').upsert(r, {onConflict:'id'});
    }
    if(rv.data && rv.data.length > 0) { ST.reviews = rv.data; LS.set('mc_revs', ST.reviews); }
    else if(rv.data && rv.data.length === 0) {
      for(const r of DEMO_REVIEWS) await sb.from('reviews').upsert(r, {onConflict:'id'});
    }

    const [cv, mn] = await Promise.all([
      sb.from('conversations').select('*'),
      sb.from('notifications').select('*'),
    ]);
    if(!cv.error && cv.data) { ST.conversations = cv.data; LS.set('mc_convos', ST.conversations); }
    if(!mn.error && mn.data) { ST.notifications = mn.data; LS.set('mc_notifs', ST.notifications); }

    // Load messages
    const {data: msgs, error: msgsErr} = await sb.from('messages').select('*');
    if(!msgsErr && msgs) {
      const grouped = {};
      for(const m of msgs) {
        if(!grouped[m.conversation_id]) grouped[m.conversation_id] = [];
        grouped[m.conversation_id].push({id:m.id, from:m.from_email, text:m.text, created_date:m.created_date});
      }
      ST.messages = grouped;
      LS.set('mc_msgs', ST.messages);
    }
    sbOk = true;
    return true;
  } catch(e) {
    console.warn('Supabase load failed, using localStorage:', e.message);
    sbOk = false;
    return false;
  }
}

async function sbUpsert(table, data) {
  if(!sb || !sbOk) return;
  try { await sb.from(table).upsert(data, {onConflict:'id'}); } catch(e) { console.warn('sbUpsert', table, e.message); }
}
async function sbDelete(table, id) {
  if(!sb || !sbOk) return;
  try { await sb.from(table).delete().eq('id', id); } catch(e) { console.warn('sbDelete', table, e.message); }
}
async function sbInsertMsg(msg, convoId) {
  if(!sb || !sbOk) return;
  try { await sb.from('messages').insert({id:msg.id, conversation_id:convoId, from_email:msg.from, text:msg.text, created_date:msg.created_date}); } catch(e) {}
}

// ═══════════════════════════════════════════
// NAV CONFIG
// ═══════════════════════════════════════════
const NAV = [
  {id:'mentors', label:'Mentors', icon:'users'},
  {id:'judges',  label:'Judges',  icon:'scale'},
  {id:'messages',label:'Messages',icon:'message-circle'},
  {id:'notifications',label:'Notifs',icon:'bell'},
  {id:'resources',label:'Resources',icon:'book-open'},
  {id:'reviews', label:'Reviews', icon:'star'},
  {id:'settings',label:'Settings',icon:'settings'},
];
const PAGE_IDS = NAV.map(n=>n.id);

// ═══════════════════════════════════════════
// ROUTING
// ═══════════════════════════════════════════
function showAuth(page) {
  document.getElementById('pg-landing').classList.remove('active');
  document.getElementById('pg-login').classList.remove('active');
  document.getElementById('pg-signup').classList.remove('active');
  document.getElementById('shell').classList.remove('active');
  document.getElementById('chat-overlay').classList.remove('active');
  if(page === 'landing') { renderLanding(); document.getElementById('pg-landing').classList.add('active'); }
  else if(page === 'login') { document.getElementById('pg-login').classList.add('active'); }
  else if(page === 'signup') { suStep=1; suRole=''; renderSu(); document.getElementById('pg-signup').classList.add('active'); }
}

function goShell(pageId) {
  document.getElementById('pg-landing').classList.remove('active');
  document.getElementById('pg-login').classList.remove('active');
  document.getElementById('pg-signup').classList.remove('active');
  document.getElementById('chat-overlay').classList.remove('active');
  document.getElementById('shell').classList.add('active');
  const idx = PAGE_IDS.indexOf(pageId);
  ST.page = pageId;
  ST.pageIdx = idx >= 0 ? idx : 0;
  renderShellNav();
  renderPage(pageId);
  setTrack(ST.pageIdx, false);
}

function navTo(pageId) {
  ST.page = pageId;
  ST.pageIdx = PAGE_IDS.indexOf(pageId);
  renderShellNav();
  renderPage(pageId);
  setTrack(ST.pageIdx, true);
}

// ═══════════════════════════════════════════
// SWIPE GESTURE
// ═══════════════════════════════════════════
let swX=0, swY=0, swDx=0, swDragging=false, swLocked=false;
const track = () => document.getElementById('pages-track');

function setTrack(idx, animate=true) {
  const t = track();
  if(!t) return;
  const isMobile = window.innerWidth < 768;
  const w = isMobile ? window.innerWidth : window.innerWidth - 224;
  if(animate) {
    t.classList.add('animating');
    setTimeout(() => t.classList.remove('animating'), 320);
  }
  t.style.transform = `translateX(${-idx * w}px)`;
}

function initSwipe() {
  const outer = document.getElementById('pages-outer');
  if(!outer) return;

  outer.addEventListener('pointerdown', e => {
    const tag = e.target.tagName;
    if(['INPUT','TEXTAREA','SELECT','BUTTON'].includes(tag)) return;
    if(e.target.closest('button')||e.target.closest('a')) return;
    if(e.target.closest('input[type="range"]')) return;
    swX = e.clientX; swY = e.clientY;
    swDx = 0; swDragging = true; swLocked = false;
  }, {passive:true});

  outer.addEventListener('pointermove', e => {
    if(!swDragging) return;
    const dx = e.clientX - swX;
    const dy = e.clientY - swY;
    if(!swLocked) {
      if(Math.abs(dx)<8 && Math.abs(dy)<8) return;
      if(Math.abs(dy) > Math.abs(dx)) { swDragging=false; return; }
      swLocked = true;
    }
    swDx = dx;
    const t = track();
    if(!t) return;
    const isMobile = window.innerWidth < 768;
    const w = isMobile ? window.innerWidth : window.innerWidth - 224;
    const base = -ST.pageIdx * w;
    const clamped = Math.max(-(PAGE_IDS.length-1)*w, Math.min(0, base+dx));
    t.style.transform = `translateX(${clamped}px)`;
  }, {passive:true});

  const onEnd = () => {
    if(!swDragging) return;
    swDragging = false;
    const threshold = 56;
    if(Math.abs(swDx) > threshold) {
      const newIdx = swDx < 0 ? Math.min(ST.pageIdx+1, PAGE_IDS.length-1) : Math.max(ST.pageIdx-1, 0);
      if(newIdx !== ST.pageIdx) {
        navTo(PAGE_IDS[newIdx]);
        return;
      }
    }
    setTrack(ST.pageIdx, true);
  };
  outer.addEventListener('pointerup', onEnd, {passive:true});
  outer.addEventListener('pointercancel', onEnd, {passive:true});
}

window.addEventListener('resize', () => setTrack(ST.pageIdx, false));

// ═══════════════════════════════════════════
// SHELL NAV RENDER
// ═══════════════════════════════════════════
function unreadMsgs() { const e=ST.currentUser?.email||''; return ST.conversations.filter(c=>c.unread_by?.includes(e)).length; }
function unreadNotifs() { const e=ST.currentUser?.email||''; return ST.notifications.filter(n=>n.user_email===e&&!n.read).length; }

function renderShellNav() {
  const uM=unreadMsgs(), uN=unreadNotifs();
  // Sidebar icons
  document.getElementById('sb-nav').innerHTML = NAV.map(n=>{
    const b = n.id==='messages'?uM:n.id==='notifications'?uN:0;
    const fullLabel = n.id==='notifications'?'Notifications':n.label;
    return `<button class="nav-item${ST.page===n.id?' active':''}" onclick="navTo('${n.id}')">
      ${ic(n.icon,18)}
      <span>${fullLabel}</span>
      ${b>0?`<span class="badge-nav">${b>9?'9+':b}</span>`:''}
    </button>`;
  }).join('');

  // Bottom nav
  document.getElementById('bottom-nav').innerHTML = NAV.map(n=>{
    const b = n.id==='messages'?uM:n.id==='notifications'?uN:0;
    return `<button class="bn-item${ST.page===n.id?' active':''}" onclick="navTo('${n.id}')">
      ${ic(n.icon,18)}
      <span style="overflow:hidden;text-overflow:ellipsis;white-space:nowrap;max-width:100%">${n.label}</span>
      ${b>0?`<span class="badge-dot">${b>9?'9+':b}</span>`:''}
    </button>`;
  }).join('');
}

// ═══════════════════════════════════════════
// PAGE RENDERERS
// ═══════════════════════════════════════════
const renderers = {
  mentors: renderMentors,
  judges: renderJudges,
  messages: renderMessages,
  notifications: renderNotifs,
  resources: renderResources,
  reviews: renderReviews,
  settings: renderSettings,
};
function renderPage(id) {
  if(renderers[id]) renderers[id]();
}

// ── LANDING ──────────────────────────────
function renderLanding() {
  setIc('landing-logo','sparkles',32,'#fff');
  setIc('ic-login','log-in',16);
  setIc('ic-useradd','user-plus',16);
  const roles=[{icon:'users',t:'Mentors',d:'Guide and teach'},{icon:'scale',t:'Judges',d:'Evaluate debates'},{icon:'book-open',t:'Coaches',d:'Build teams'},{icon:'graduation-cap',t:'Teachers',d:'Educate students'}];
  document.getElementById('role-tiles').innerHTML = roles.map(r=>`
    <div class="card-s" style="border-radius:.75rem;padding:1rem;text-align:center">
      ${ic(r.icon,28,'hsl(221 83% 53%)',1.75)}
      <h3 style="font-size:.875rem;font-weight:600;margin-top:.5rem">${r.t}</h3>
      <p style="font-size:.75rem;color:hsl(var(--muted-fg));margin-top:.125rem">${r.d}</p>
    </div>`).join('');
  document.getElementById('landing-reviews').innerHTML = buildReviewsSection();
}

function buildReviewsSection() {
  const fp = ST.reviews.filter(r=>r.front_page);
  const show = fp.length>0?fp.slice(0,3):ST.reviews.slice(0,3);
  const avg = ST.reviews.length>0?Math.round(ST.reviews.reduce((s,r)=>s+r.satisfaction_pct,0)/ST.reviews.length):0;
  return `<div style="display:flex;flex-direction:column;gap:1rem">
    <div class="card" style="padding:1.25rem">
      <div style="display:flex;align-items:center;gap:.75rem;margin-bottom:.75rem">${ic('thumbs-up',20,'hsl(221 83% 53%)')} <h3 style="font-size:1.125rem">Community Satisfaction</h3></div>
      <div style="display:flex;align-items:baseline;gap:.5rem;margin-bottom:.5rem"><span style="font-size:1.875rem;font-weight:700;font-family:'DM Serif Display',serif">${avg}%</span><span style="font-size:.875rem;color:hsl(var(--muted-fg))">would recommend</span></div>
      <div class="sat-bar"><div class="sat-fill" style="width:${avg}%"></div></div>
      <p style="font-size:.75rem;color:hsl(var(--muted-fg));margin-top:.5rem">Based on ${ST.reviews.length} reviews</p>
    </div>
    ${show.map(r=>`<div class="rev-card">
      <div style="display:flex;gap:.75rem">
        <div class="av av-xs">${r.user_name.split(' ').map(n=>n[0]).join('').slice(0,2)}</div>
        <div style="flex:1;min-width:0">
          <div style="display:flex;justify-content:space-between;align-items:flex-start">
            <div><span style="font-weight:600;font-size:.875rem">${H(r.user_name)}</span><span class="badge b-role" style="margin-left:.5rem;font-size:.625rem">${H(r.user_role)}</span></div>
            <div>${stars(r.rating)}</div>
          </div>
          <p style="font-size:.875rem;color:hsl(var(--muted-fg));margin-top:.375rem;line-height:1.5">"${H(r.text)}"</p>
          <div style="display:flex;justify-content:space-between;margin-top:.5rem">
            <span style="font-size:.75rem;color:hsl(var(--muted-fg))">${fmtDate(r.date)}</span>
            <span style="font-size:.75rem;font-weight:600;color:hsl(${r.satisfaction_pct>90?'142 71% 45%':'221 83% 53%'})">${r.satisfaction_pct}% satisfied</span>
          </div>
        </div>
      </div>
    </div>`).join('')}
  </div>`;
}

// ── PERSON LIST (Mentors / Judges) ──────────
function renderPersonList(role, slotId) {
  const s = role==='mentor'?ST.mSearch:ST.jSearch;
  const lf = role==='mentor'?ST.mLoc:ST.jLoc;
  const icon = role==='mentor'?'users':'scale';
  const title = role==='mentor'?'Mentors':'Judges';
  const ph = role==='mentor'?'Search mentors…':'Search judges…';

  const people = ST.users.filter(u=>u.role===role && u.email_verified && u.email!==ST.currentUser?.email);
  const filtered = people.filter(p=>{
    const q=`${p.first_name} ${p.last_name} ${p.description||''}`.toLowerCase().includes(s.toLowerCase());
    return q && (!lf || p.location===lf);
  });

  document.getElementById(slotId).innerHTML = `
    <div class="topbar">
      <div style="padding:1rem 1.25rem">
        <div style="display:flex;align-items:center;gap:.75rem">${ic(icon,20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">${title}</h1></div>
        <div style="display:flex;flex-direction:column;gap:.5rem;margin-top:.75rem">
          <div style="position:relative">
            <span style="position:absolute;left:.75rem;top:50%;transform:translateY(-50%);pointer-events:none;display:flex">${ic('search',16,'hsl(215 16% 47%)')}</span>
            <input class="inp" style="padding-left:2.25rem" placeholder="${ph}" value="${H(s)}"
              oninput="ST.${role==='mentor'?'mSearch':'jSearch'}=this.value;${role==='mentor'?'renderMentors':'renderJudges'}()"/>
          </div>
          <div style="position:relative">
            <span style="position:absolute;left:.75rem;top:.75rem;pointer-events:none;display:flex">${ic('map-pin',16,'hsl(215 16% 47%)')}</span>
            <input class="inp" style="padding-left:2.25rem" id="${role}-loc-inp" placeholder="Filter by location…" value="${H(lf)}"
              oninput="updateLocDrop('${role}',this.value)"
              onfocus="showLocDrop('${role}')"
              onblur="setTimeout(()=>hideLocDrop('${role}'),200)"/>
            ${lf?`<button onclick="clearLoc('${role}')" style="position:absolute;right:.5rem;top:50%;transform:translateY(-50%);font-size:.75rem;color:hsl(var(--muted-fg))">✕</button>`:''}
            <div id="${role}-loc-drop" class="ac-drop" style="display:none"></div>
          </div>
        </div>
      </div>
    </div>
    <div style="padding:1.25rem">
      <div class="g2" style="max-width:1100px;margin:0 auto">
        ${filtered.length===0?`<div style="text-align:center;padding:3rem 0;grid-column:1/-1">${ic(icon,48,'hsl(215 16% 47%/.3)',1.5)}<p style="font-weight:500;color:hsl(var(--muted-fg));margin-top:.75rem">No ${role}s found</p></div>`:filtered.map(p=>buildPersonCard(p)).join('')}
      </div>
    </div>`;
}
function renderMentors(){ renderPersonList('mentor','slot-mentors'); }
function renderJudges(){ renderPersonList('judge','slot-judges'); }

// Location dropdowns
function showLocDrop(r){
  const inp=document.getElementById(`${r}-loc-inp`);
  if(!inp) return;
  const all=[...new Set([...US_STATES,...COUNTRIES])].sort();
  const q=inp.value.toLowerCase();
  const f=q?all.filter(l=>l.toLowerCase().includes(q)).slice(0,30):all.slice(0,30);
  const dd=document.getElementById(`${r}-loc-drop`);
  if(!dd) return;
  dd.innerHTML=f.map(l=>`<button class="ac-opt" onmousedown="selLoc('${r}','${H(l)}')">${H(l)}</button>`).join('');
  dd.style.display=f.length?'block':'none';
}
function hideLocDrop(r){const d=document.getElementById(`${r}-loc-drop`);if(d)d.style.display='none'}
function updateLocDrop(r,v){if(r==='mentor')ST.mLoc='';else ST.jLoc='';showLocDrop(r)}
function selLoc(r,loc){
  if(r==='mentor'){ST.mLoc=loc;renderMentors();}else{ST.jLoc=loc;renderJudges();}
}
function clearLoc(r){
  if(r==='mentor'){ST.mLoc='';renderMentors();}else{ST.jLoc='';renderJudges();}
}

function buildPersonCard(p) {
  const me=ST.currentUser?.email||'';
  const initials=`${(p.first_name||'?')[0]}${(p.last_name||'?')[0]}`;
  const convo=ST.conversations.find(c=>c.participants.includes(me)&&c.participants.includes(p.email));
  const hidden=convo?.hidden_by?.includes(me);
  const btn = convo&&!hidden?'Open':convo&&hidden?'Reopen':'Message';
  return `<div class="card-s person-card" style="padding:1.25rem">
    <div class="pcard-line"></div>
    <div style="display:flex;align-items:flex-start;gap:1rem;margin-bottom:.75rem">
      <div class="av av-md">${H(initials)}</div>
      <div style="flex:1;min-width:0">
        <div style="font-weight:700;font-size:1rem;line-height:1.2">${H(p.first_name)} ${H(p.last_name)}</div>
        <span class="badge b-role" style="margin-top:.25rem">${H(p.role)}</span>
        <div style="display:flex;flex-wrap:wrap;gap:.375rem;margin-top:.5rem">
          ${p.location?`<span class="pill">${ic('map-pin',12)} ${H(p.location)}</span>`:''}
          ${p.school?`<span class="pill">${ic('graduation-cap',12)} ${H(p.school)}</span>`:''}
        </div>
      </div>
    </div>
    ${p.description?`<p style="font-size:.875rem;color:hsl(var(--muted-fg));line-height:1.5" class="clamp2">${H(p.description)}</p>`:''}
    <div style="display:flex;align-items:center;justify-content:space-between;margin-top:1rem;padding-top:.75rem;border-top:1px solid hsl(var(--mc50))">
      <div style="display:flex;gap:.375rem;flex-wrap:wrap">
        ${p.available_for_hire?`<span class="badge b-avail">Available</span>`:`<span class="badge b-unavail">Unavailable</span>`}
        ${p.tabroom_linked?`<span class="badge" style="background:hsl(var(--mcg50));color:hsl(var(--mcg700));border:1px solid hsl(142 76% 72%)">${ic('check',12)} Tabroom</span>`:''}
      </div>
      ${p.available_for_hire?`<div style="display:flex;gap:.375rem">
        <button onclick="openConvo('${H(p.email)}')" class="btn btn-p btn-sm" style="gap:.375rem;width:auto">
          ${ic('message-circle',14)} ${btn}
        </button>
        ${convo&&!hidden?`<button onclick="hideConvoCard('${convo.id}')" class="btn btn-sm" style="border:1px solid hsl(var(--mc200));color:hsl(var(--mc600));width:auto">
          ${ic('eye-off',14)} Hide
        </button>`:''}
      </div>`:''}
    </div>
  </div>`;
}

function openConvo(email) {
  const me=ST.currentUser;
  if(!me) return;
  const person=ST.users.find(u=>u.email===email);
  if(!person) return;
  let c=ST.conversations.find(cv=>cv.participants.includes(me.email)&&cv.participants.includes(email));
  if(c) {
    if(c.hidden_by?.includes(me.email)){
      c={...c,hidden_by:c.hidden_by.filter(e=>e!==me.email)};
      ST.conversations=ST.conversations.map(x=>x.id===c.id?c:x);
      save(); sbUpsert('conversations',c);
    }
  } else {
    c={id:'c_'+Date.now(),participants:[me.email,email],participant_names:{[me.email]:`${me.first_name} ${me.last_name}`,[email]:`${person.first_name} ${person.last_name}`},last_message:'',last_message_date:new Date().toISOString(),unread_by:[],hidden_by:[]};
    ST.conversations=[...ST.conversations,c];
    save(); sbUpsert('conversations',c);
  }
  ST.convoId=c.id;
  showChatOverlay();
}
function hideConvoCard(id){
  const me=ST.currentUser?.email;
  ST.conversations=ST.conversations.map(c=>c.id===id?{...c,hidden_by:[...(c.hidden_by||[]),me]}:c);
  save(); sbUpsert('conversations',ST.conversations.find(c=>c.id===id));
  renderMentors(); renderJudges();
}

// ── MESSAGES ─────────────────────────────
function renderMessages(){
  const me=ST.currentUser?.email||'';
  const vis=ST.conversations.filter(c=>c.participants.includes(me)&&!c.hidden_by?.includes(me))
    .sort((a,b)=>new Date(b.last_message_date)-new Date(a.last_message_date));
  document.getElementById('slot-messages').innerHTML=`
    <div class="topbar">
      <div style="padding:1rem 1.25rem;display:flex;align-items:center;gap:.75rem">${ic('message-circle',20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">Messages</h1></div>
    </div>
    <div style="padding:1.25rem">
      <div style="max-width:700px;margin:0 auto">
        ${vis.length===0?`<div style="text-align:center;padding:3rem 0">${ic('message-circle',48,'hsl(212 96% 78%)',1.5)}<p style="font-weight:500;color:hsl(var(--muted-fg));margin-top:.75rem">No conversations yet</p><p style="font-size:.875rem;color:hsl(var(--mc400));margin-top:.25rem">Start by messaging a mentor or judge</p></div>`:vis.map(c=>{
          const oe=c.participants.find(e=>e!==me)||'';
          const on=c.participant_names?.[oe]||oe;
          const ini=on.split(' ').map(n=>n[0]).join('').slice(0,2);
          const ur=c.unread_by?.includes(me);
          const ds=c.last_message_date?new Date(c.last_message_date).toLocaleDateString([],{month:'short',day:'numeric'}):'';
          return `<div onclick="msgOpenChat('${c.id}')" class="card-s" style="padding:1rem;cursor:pointer;margin-bottom:.75rem${ur?';background:hsl(var(--mc50)/.6)':''}">
            <div style="display:flex;align-items:center;gap:.75rem">
              <div class="av av-sm">${H(ini)}</div>
              <div style="flex:1;min-width:0">
                <div style="display:flex;justify-content:space-between">
                  <span style="font-size:.875rem;font-weight:${ur?700:600}">${H(on)}</span>
                  <span style="font-size:.75rem;color:hsl(var(--muted-fg))">${ds}</span>
                </div>
                <div style="display:flex;align-items:center;justify-content:space-between;margin-top:.125rem">
                  <span style="font-size:.875rem;color:hsl(var(--muted-fg));overflow:hidden;text-overflow:ellipsis;white-space:nowrap;max-width:200px;font-weight:${ur?500:400}">${H(c.last_message||'No messages yet')}</span>
                  <div style="display:flex;align-items:center;gap:.5rem;flex-shrink:0">
                    ${ur?'<span style="width:10px;height:10px;border-radius:50%;background:hsl(var(--primary))"></span>':''}
                    <button onclick="event.stopPropagation();hideMsgConvo('${c.id}')" style="padding:.25rem;border-radius:.5rem;color:hsl(var(--muted-fg))" title="Hide">${ic('eye-off',16)}</button>
                    ${ic('chevron-right',16,'hsl(213 94% 68%)')}
                  </div>
                </div>
              </div>
            </div>
          </div>`;
        }).join('')}
      </div>
    </div>`;
}
function msgOpenChat(id){
  const me=ST.currentUser?.email;
  ST.conversations=ST.conversations.map(c=>c.id===id?{...c,unread_by:(c.unread_by||[]).filter(e=>e!==me)}:c);
  save(); sbUpsert('conversations',ST.conversations.find(c=>c.id===id));
  ST.convoId=id;
  showChatOverlay();
}
function hideMsgConvo(id){
  const me=ST.currentUser?.email;
  ST.conversations=ST.conversations.map(c=>c.id===id?{...c,hidden_by:[...(c.hidden_by||[]),me]}:c);
  save(); sbUpsert('conversations',ST.conversations.find(c=>c.id===id));
  renderMessages();
}

// ── CHAT OVERLAY ─────────────────────────
function showChatOverlay(){
  document.getElementById('chat-overlay').classList.add('active');
  renderChat();
}
function closeChat(){
  document.getElementById('chat-overlay').classList.remove('active');
  renderMessages();
  renderShellNav();
}
function renderChat(){
  const me=ST.currentUser?.email||'';
  const c=ST.conversations.find(x=>x.id===ST.convoId);
  const msgs=(ST.convoId?ST.messages[ST.convoId]:[])||[];
  const oe=c?.participants.find(e=>e!==me)||'';
  const on=c?.participant_names?.[oe]||oe;
  const ini=on.split(' ').map(n=>n[0]).join('').slice(0,2);
  document.getElementById('chat-av').textContent=ini;
  document.getElementById('chat-name').textContent=on;
  setIc('chat-back-btn','chevron-left',20,'hsl(213 94% 68%)');
  setIc('chat-send-btn','send',16,'#fff');
  const mc=document.getElementById('chat-msgs');
  mc.innerHTML=msgs.length===0?`<div style="text-align:center;color:hsl(var(--muted-fg));font-size:.875rem;margin:auto">Say hello! 👋</div>`:msgs.map(m=>{
    const mine=m.from===me;
    const t=new Date(m.created_date).toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'});
    return `<div style="display:flex;justify-content:${mine?'flex-end':'flex-start'}">
      <div style="max-width:74%;display:inline-flex;flex-direction:column;align-items:${mine?'flex-end':'flex-start'}">
        <div class="${mine?'bubble-me':'bubble-them'}" style="padding:.625rem .875rem;font-size:.875rem;line-height:1.5;word-break:break-word">${H(m.text)}</div>
        <span style="font-size:.6875rem;margin-top:.25rem;color:hsl(213 94% 68%)">${t}</span>
      </div>
    </div>`;
  }).join('');
  mc.scrollTop=mc.scrollHeight;
}
function sendMsg(){
  const inp=document.getElementById('chat-inp');
  const text=inp?.value.trim();
  if(!text||!ST.convoId||!ST.currentUser) return;
  const msg={id:'m_'+Date.now(),from:ST.currentUser.email,text,created_date:new Date().toISOString()};
  if(!ST.messages[ST.convoId]) ST.messages[ST.convoId]=[];
  ST.messages[ST.convoId]=[...ST.messages[ST.convoId],msg];
  ST.conversations=ST.conversations.map(c=>{
    if(c.id===ST.convoId){
      const others=c.participants.filter(e=>e!==ST.currentUser.email);
      return {...c,last_message:msg.text,last_message_date:msg.created_date,unread_by:[...(c.unread_by||[]),...others.filter(e=>!c.unread_by?.includes(e))]};
    }
    return c;
  });
  save();
  sbInsertMsg(msg, ST.convoId);
  sbUpsert('conversations', ST.conversations.find(c=>c.id===ST.convoId));
  inp.value='';
  renderChat();
}

// ── NOTIFICATIONS ─────────────────────────
function renderNotifs(){
  const me=ST.currentUser?.email||'';
  const myN=ST.notifications.filter(n=>n.user_email===me).sort((a,b)=>new Date(b.created_date)-new Date(a.created_date));
  const uc=myN.filter(n=>!n.read).length;
  const icoMap={message:'message-circle',hire_request:'user-plus',new_resource:'book-open',default:'bell'};
  document.getElementById('slot-notifications').innerHTML=`
    <div class="topbar">
      <div style="padding:1rem 1.25rem;display:flex;align-items:center;justify-content:space-between">
        <div style="display:flex;align-items:center;gap:.75rem">${ic('bell',20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">Notifications</h1>${uc>0?`<span class="badge" style="background:hsl(var(--mc100));color:hsl(var(--mc800))">${uc}</span>`:''}</div>
        ${uc>0?`<button onclick="markAllRead()" style="font-size:.875rem;color:hsl(var(--primary));font-weight:500">Mark all read</button>`:''}
      </div>
    </div>
    <div style="padding:1.25rem"><div style="max-width:700px;margin:0 auto">
      ${myN.length===0?`<div style="text-align:center;padding:3rem 0">${ic('bell',48,'hsl(212 96% 78%)',1.5)}<p style="font-weight:500;color:hsl(var(--muted-fg));margin-top:.75rem">No notifications</p></div>`:myN.map(n=>{
        const ico=icoMap[n.type]||'bell';
        const ds=new Date(n.created_date).toLocaleDateString([],{month:'short',day:'numeric'});
        return `<div onclick="notifClick('${n.id}')" class="card-s" style="padding:1rem;cursor:pointer;margin-bottom:.75rem${!n.read?';background:hsl(var(--mc50)/.6)':''}">
          <div style="display:flex;gap:.75rem">
            <div style="width:40px;height:40px;border-radius:50%;display:flex;align-items:center;justify-content:center;flex-shrink:0;background:hsl(var(${!n.read?'--primary':'--mc100'}))">
              ${ic(ico,16,!n.read?'#fff':'hsl(221 83% 53%)')}
            </div>
            <div style="flex:1;min-width:0">
              <div style="display:flex;justify-content:space-between;align-items:flex-start">
                <span style="font-weight:600;font-size:.875rem">${H(n.title)}</span>
                <div style="display:flex;align-items:center;gap:4px;flex-shrink:0">
                  <span style="font-size:.75rem;color:hsl(var(--muted-fg))">${ds}</span>
                  ${!n.read?`<button onclick="event.stopPropagation();markRead('${n.id}')" style="padding:.25rem;color:hsl(var(--mc400))">${ic('check',16)}</button>`:''}
                  <button onclick="event.stopPropagation();delNotif('${n.id}')" style="padding:.25rem;color:hsl(var(--muted-fg))">${ic('trash-2',16)}</button>
                </div>
              </div>
              <p style="font-size:.875rem;color:hsl(var(--muted-fg));margin-top:.25rem">${H(n.message)}</p>
              ${n.from_user_name?`<p style="font-size:.75rem;color:hsl(213 94% 68%);margin-top:.25rem">From: ${H(n.from_user_name)}</p>`:''}
            </div>
          </div>
        </div>`;
      }).join('')}
    </div></div>`;
}
function markRead(id){
  ST.notifications=ST.notifications.map(n=>n.id===id?{...n,read:true}:n);
  save(); sbUpsert('notifications',ST.notifications.find(n=>n.id===id));
  renderNotifs(); renderShellNav();
}
function markAllRead(){
  const me=ST.currentUser?.email;
  ST.notifications=ST.notifications.map(n=>n.user_email===me?{...n,read:true}:n);
  save();
  const myN=ST.notifications.filter(n=>n.user_email===me);
  myN.forEach(n=>sbUpsert('notifications',n));
  renderNotifs(); renderShellNav();
}
function delNotif(id){
  ST.notifications=ST.notifications.filter(n=>n.id!==id);
  save(); sbDelete('notifications',id);
  renderNotifs();
}
function notifClick(id){
  const n=ST.notifications.find(x=>x.id===id);
  if(!n) return;
  markRead(id);
  if((n.type==='message'||n.type==='hire_request')&&n.reference_id){
    ST.convoId=n.reference_id; showChatOverlay();
  } else if(n.type==='new_resource') navTo('resources');
}

// ── RESOURCES ─────────────────────────────
function renderResources(){
  const admin=isAdmin(ST.currentUser);
  const cats=['debate','public_speaking','coaching','judging','general'];
  const f=ST.resCat?ST.resources.filter(r=>r.category===ST.resCat):ST.resources;

  if(ST.selRes){
    const r=ST.selRes;
    const icoMap={video:'video',document:'file-text',link:'link-2'};
    const ico=icoMap[r.type]||'book-open';
    document.getElementById('slot-resources').innerHTML=`
      <div class="topbar"><div style="padding:1rem 1.25rem">
        <button onclick="ST.selRes=null;renderResources()" style="display:flex;align-items:center;gap:.375rem;font-size:.875rem;color:hsl(var(--primary));font-weight:500;margin-bottom:.5rem">
          ${ic('arrow-left',16)} Back to Resources
        </button>
        <h1 style="font-size:1.5rem">${H(r.title)}</h1>
      </div></div>
      <div style="padding:1.25rem"><div class="card-s" style="padding:1.5rem;max-width:700px;margin:0 auto">
        <div style="display:flex;align-items:center;gap:.875rem;margin-bottom:1rem">
          <div style="width:56px;height:56px;border-radius:.75rem;background:hsl(var(--secondary));display:flex;align-items:center;justify-content:center;flex-shrink:0">
            ${ic(ico,28,'hsl(221 83% 53%)',1.75)}
          </div>
          <div>
            <div style="display:flex;gap:.5rem;flex-wrap:wrap">
              <span class="badge cat-${r.category||'general'}">${(r.category||'').replace('_',' ')}</span>
              <span class="badge" style="border:1px solid hsl(var(--border));color:hsl(var(--primary))">${r.type}</span>
            </div>
            ${r.posted_by_name?`<p style="font-size:.75rem;color:hsl(var(--muted-fg));margin-top:.375rem">By ${H(r.posted_by_name)}</p>`:''}
          </div>
        </div>
        ${r.description?`<p style="color:hsl(var(--muted-fg));line-height:1.6;margin-bottom:1rem">${H(r.description)}</p>`:''}
        ${r.url&&r.url!=='#'?`<a href="${H(r.url)}" target="_blank" rel="noopener" class="btn btn-p" style="text-decoration:none;width:auto;padding:.625rem 1.25rem">
          ${ic('external-link',16)} Open Resource
        </a>`:''}
      </div></div>`;
    return;
  }

  document.getElementById('slot-resources').innerHTML=`
    <div class="topbar"><div style="padding:1rem 1.25rem">
      <div style="display:flex;align-items:center;justify-content:space-between">
        <div style="display:flex;align-items:center;gap:.75rem">${ic('book-open',20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">Resources</h1></div>
        ${admin?`<button onclick="ST.showResForm=true;renderResources()" class="btn btn-p btn-sm">${ic('plus',16)} New</button>`:''}
      </div>
      <div style="margin-top:.75rem">
        <select class="inp" style="width:auto" onchange="ST.resCat=this.value;renderResources()">
          <option value="">All Categories</option>
          ${cats.map(c=>`<option value="${c}"${ST.resCat===c?' selected':''}>${c.replace('_',' ')}</option>`).join('')}
        </select>
      </div>
    </div></div>
    ${ST.showResForm?`<div style="padding:1.25rem 1.25rem 0"><div class="card-s" style="padding:1.25rem;max-width:700px;margin:0 auto">
      <div style="display:flex;justify-content:space-between;margin-bottom:.75rem">
        <h3 style="font-size:1rem">New Resource</h3>
        <button onclick="ST.showResForm=false;renderResources()" style="color:hsl(var(--muted-fg))">${ic('x',18)}</button>
      </div>
      <div style="display:flex;flex-direction:column;gap:.75rem">
        <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Title *</label><input class="inp" id="r-title" placeholder="Resource title"/></div>
        <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Description</label><textarea class="inp" id="r-desc" rows="3" style="resize:none"></textarea></div>
        <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">URL</label><input class="inp" id="r-url" placeholder="https://..."/></div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:.75rem">
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Category</label>
            <select class="inp" id="r-cat">${cats.map(c=>`<option value="${c}">${c.replace('_',' ')}</option>`).join('')}</select></div>
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Type</label>
            <select class="inp" id="r-type"><option value="document">document</option><option value="video">video</option><option value="link">link</option></select></div>
        </div>
        <button class="btn btn-p" onclick="addResource()">Add Resource</button>
      </div>
    </div></div>`:''}
    <div style="padding:1.25rem"><div class="g3" style="max-width:1100px;margin:0 auto">
      ${f.length===0?`<div style="text-align:center;padding:3rem 0;grid-column:1/-1">${ic('book-open',48,'hsl(215 16% 47%/.3)',1.5)}<p style="font-weight:500;color:hsl(var(--muted-fg));margin-top:.75rem">No resources</p></div>`:f.map(r=>{
        const icoMap={video:'video',document:'file-text',link:'link-2'};
        const ico=icoMap[r.type]||'book-open';
        return `<div class="card-s" style="padding:1.25rem;cursor:pointer" onclick="ST.selRes=ST.resources.find(x=>x.id==='${r.id}');renderResources()">
          <div style="display:flex;gap:.875rem;margin-bottom:.75rem">
            <div style="width:44px;height:44px;border-radius:.75rem;background:hsl(var(--secondary));display:flex;align-items:center;justify-content:center;flex-shrink:0">
              ${ic(ico,22,'hsl(221 83% 53%)',1.75)}
            </div>
            <div style="flex:1;min-width:0">
              <h3 style="font-size:.875rem;font-weight:600;line-height:1.3" class="clamp2">${H(r.title)}</h3>
              <span class="badge cat-${r.category||'general'}" style="margin-top:.25rem">${(r.category||'').replace('_',' ')}</span>
            </div>
          </div>
          ${r.description?`<p style="font-size:.8125rem;color:hsl(var(--muted-fg));line-height:1.5" class="clamp2">${H(r.description)}</p>`:''}
          <div style="display:flex;justify-content:space-between;align-items:center;margin-top:.75rem;padding-top:.625rem;border-top:1px solid hsl(var(--mc50))">
            <span style="font-size:.75rem;color:hsl(var(--muted-fg))">${r.posted_by_name?H(r.posted_by_name):''}</span>
            ${admin?`<button onclick="event.stopPropagation();delResource('${r.id}')" style="color:hsl(var(--muted-fg))">${ic('trash-2',14)}</button>`:''}
          </div>
        </div>`;
      }).join('')}
    </div></div>`;
}
function addResource(){
  const t=document.getElementById('r-title')?.value.trim();
  if(!t) return;
  const r={id:'r_'+Date.now(),title:t,description:document.getElementById('r-desc')?.value.trim()||'',url:document.getElementById('r-url')?.value.trim()||'#',category:document.getElementById('r-cat')?.value||'debate',type:document.getElementById('r-type')?.value||'document',posted_by_name:`${ST.currentUser?.first_name} ${ST.currentUser?.last_name}`,created_date:new Date().toISOString()};
  ST.resources=[...ST.resources,r];
  ST.showResForm=false;
  save(); sbUpsert('resources',r);
  renderResources();
}
function delResource(id){
  if(!confirm('Delete this resource?')) return;
  ST.resources=ST.resources.filter(r=>r.id!==id);
  save(); sbDelete('resources',id);
  renderResources();
}

// ── REVIEWS ──────────────────────────────
function stars(n,sz=12){return Array.from({length:5}).map((_,i)=>`<svg width="${sz}" height="${sz}" viewBox="0 0 24 24" fill="${i<n?'#fbbf24':'none'}" stroke="${i<n?'#fbbf24':'#d1d5db'}" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>`).join('')}
function renderReviews(){
  const admin=isAdmin(ST.currentUser);
  const me=ST.currentUser?.email;
  const sorted=[...ST.reviews].sort((a,b)=>new Date(b.date)-new Date(a.date));
  const avg=ST.reviews.length>0?Math.round(ST.reviews.reduce((s,r)=>s+r.satisfaction_pct,0)/ST.reviews.length):0;
  document.getElementById('slot-reviews').innerHTML=`
    <div class="topbar"><div style="padding:1rem 1.25rem;display:flex;align-items:center;justify-content:space-between">
      <div style="display:flex;align-items:center;gap:.75rem">${ic('star',20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">Reviews</h1></div>
      <button onclick="ST.showRevForm=!ST.showRevForm;ST.editingRev=null;ST.revRating=5;ST.revText='';ST.revSat=95;renderReviews()" class="btn btn-p btn-sm">${ic('message-square-plus',16)} Write Review</button>
    </div></div>
    <div style="padding:1.25rem;max-width:800px;margin:0 auto">
      <!-- Summary -->
      <div class="card" style="padding:1.25rem;margin-bottom:1.25rem">
        <div style="display:flex;align-items:center;justify-content:space-between">
          <div>
            <div style="display:flex;align-items:baseline;gap:.5rem"><span style="font-size:2.5rem;font-weight:700;font-family:'DM Serif Display',serif">${avg}%</span><span style="color:hsl(var(--muted-fg))">satisfaction</span></div>
            <div class="sat-bar" style="width:200px;margin-top:.375rem"><div class="sat-fill" style="width:${avg}%"></div></div>
            <p style="font-size:.75rem;color:hsl(var(--muted-fg));margin-top:.375rem">From ${ST.reviews.length} reviews</p>
          </div>
          ${ic('thumbs-up',48,'hsl(213 97% 87%)',1.5)}
        </div>
      </div>
      ${ST.showRevForm?`
      <div class="card-s" style="padding:1.25rem;margin-bottom:1.25rem;animation:slideIn .25s ease both">
        <div style="display:flex;justify-content:space-between;margin-bottom:.75rem">
          <h3 style="font-size:1rem">${ST.editingRev?'Edit Review':'Write a Review'}</h3>
          <button onclick="ST.showRevForm=false;ST.editingRev=null;renderReviews()" style="color:hsl(var(--muted-fg))">${ic('x',18)}</button>
        </div>
        <div style="display:flex;flex-direction:column;gap:.75rem">
          <div>
            <label style="font-size:.875rem;font-weight:500;margin-bottom:.375rem;display:block">Rating</label>
            <div style="display:flex;gap:.25rem">
              ${Array.from({length:5}).map((_,i)=>`<button onclick="ST.revRating=${i+1};renderReviews()" style="padding:2px;background:none;border:none;cursor:pointer;transition:transform .1s" onmouseover="this.style.transform='scale(1.2)'" onmouseout="this.style.transform=''">
                <svg width="24" height="24" viewBox="0 0 24 24" fill="${i<ST.revRating?'#fbbf24':'none'}" stroke="${i<ST.revRating?'#fbbf24':'#d1d5db'}" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 15.09 8.26 22 9.27 17 14.14 18.18 21.02 12 17.77 5.82 21.02 7 14.14 2 9.27 8.91 8.26 12 2"/></svg>
              </button>`).join('')}
            </div>
          </div>
          <div>
            <label style="font-size:.875rem;font-weight:500;margin-bottom:.25rem;display:block">Satisfaction: <strong>${ST.revSat}%</strong></label>
            <input type="range" min="1" max="100" value="${ST.revSat}" style="width:100%;accent-color:hsl(var(--primary))"
              oninput="ST.revSat=+this.value;document.getElementById('rev-sat-lbl').textContent=this.value+'%'" onpointerdown="event.stopPropagation()"/>
            <span id="rev-sat-lbl" style="font-size:.75rem;color:hsl(var(--muted-fg))">${ST.revSat}%</span>
          </div>
          <div>
            <label style="font-size:.875rem;font-weight:500;margin-bottom:.25rem;display:block">Your review *</label>
            <textarea id="rev-txt" class="inp" rows="3" style="resize:none" placeholder="Share your experience…">${H(ST.revText)}</textarea>
          </div>
          <div style="display:flex;gap:.5rem">
            <button class="btn btn-p" style="flex:1" onclick="submitReview()">${ST.editingRev?'Save Changes':'Submit Review'}</button>
            <button class="btn btn-o" style="width:auto;padding:.75rem 1rem" onclick="ST.showRevForm=false;ST.editingRev=null;renderReviews()">Cancel</button>
          </div>
        </div>
      </div>`:'' }
      <div style="display:flex;flex-direction:column;gap:.75rem">
        ${sorted.map(r=>`<div class="rev-card${r.front_page?' featured':''}">
          <div style="display:flex;gap:.75rem">
            <div class="av av-xs">${r.user_name.split(' ').map(n=>n[0]).join('').slice(0,2)}</div>
            <div style="flex:1;min-width:0">
              <div style="display:flex;align-items:flex-start;justify-content:space-between">
                <div style="display:flex;flex-wrap:wrap;align-items:center;gap:.375rem">
                  <span style="font-weight:600;font-size:.875rem">${H(r.user_name)}</span>
                  <span class="badge b-role" style="font-size:.625rem">${H(r.user_role)}</span>
                  ${r.front_page?`<span class="badge" style="background:#fffbeb;color:#92400e;border:1px solid #fcd34d;font-size:.625rem">${ic('crown',10,'#92400e')} Featured</span>`:''}
                </div>
                <div style="display:flex;align-items:center;gap:2px;flex-shrink:0">
                  ${stars(r.rating)}
                  ${r.user_email===me||admin?`<button onclick="startEditRev('${r.id}')" style="padding:.25rem;color:hsl(var(--muted-fg));margin-left:4px">${ic('pencil',13)}</button>`:''}
                  ${r.user_email===me||admin?`<button onclick="delReview('${r.id}')" style="padding:.25rem;color:hsl(var(--muted-fg))">${ic('trash-2',13)}</button>`:''}
                  ${admin&&!r.front_page?`<button onclick="featureReview('${r.id}')" style="padding:.25rem;color:hsl(var(--mcg600))" title="Feature">${ic('crown',14,'hsl(142 71% 45%)')}</button>`:''}
                  ${admin&&r.front_page?`<button onclick="unfeatureReview('${r.id}')" style="padding:.25rem;color:hsl(var(--muted-fg))" title="Unfeature">${ic('x',14)}</button>`:''}
                </div>
              </div>
              <p style="font-size:.875rem;color:hsl(var(--muted-fg));margin-top:.375rem;line-height:1.5">"${H(r.text)}"</p>
              <div style="display:flex;justify-content:space-between;margin-top:.5rem">
                <span style="font-size:.75rem;color:hsl(var(--muted-fg))">${fmtDate(r.date)}</span>
                <span style="font-size:.75rem;font-weight:600;color:hsl(${r.satisfaction_pct>90?'142 71% 45%':'221 83% 53%'})">${r.satisfaction_pct}% satisfied</span>
              </div>
            </div>
          </div>
        </div>`).join('')}
      </div>
    </div>`;
}
function submitReview(){
  const text=document.getElementById('rev-txt')?.value.trim();
  if(!text||!ST.currentUser) return;
  if(ST.editingRev){
    ST.reviews=ST.reviews.map(r=>r.id===ST.editingRev.id?{...r,rating:ST.revRating,text,satisfaction_pct:ST.revSat}:r);
    const updated=ST.reviews.find(r=>r.id===ST.editingRev.id);
    sbUpsert('reviews',updated);
    ST.editingRev=null;
  } else {
    const r={id:'rv_'+Date.now(),user_name:`${ST.currentUser.first_name} ${ST.currentUser.last_name}`,user_email:ST.currentUser.email,user_role:ST.currentUser.role,rating:ST.revRating,text,date:new Date().toISOString().split('T')[0],satisfaction_pct:ST.revSat,front_page:false};
    ST.reviews=[...ST.reviews,r];
    sbUpsert('reviews',r);
  }
  save(); ST.showRevForm=false; ST.revText=''; renderReviews();
}
function startEditRev(id){
  const r=ST.reviews.find(x=>x.id===id);
  if(!r) return;
  ST.editingRev=r; ST.revRating=r.rating; ST.revText=r.text; ST.revSat=r.satisfaction_pct; ST.showRevForm=true;
  renderReviews();
  setTimeout(()=>{const el=document.getElementById('rev-txt');if(el)el.value=r.text;},50);
}
function delReview(id){
  if(!confirm('Delete this review?')) return;
  ST.reviews=ST.reviews.filter(r=>r.id!==id);
  save(); sbDelete('reviews',id); renderReviews();
}
function featureReview(id){
  const r=ST.reviews.find(x=>x.id===id);
  if(!r) return;
  ST.reviews=ST.reviews.map(x=>x.id===id?{...x,front_page:true}:x);
  save(); sbUpsert('reviews',{...r,front_page:true});
  if(r.user_email){
    const n={id:'n_'+Date.now(),user_email:r.user_email,type:'default',title:'⭐ Review Featured!',message:'Your review has been selected for the front page!',read:false,created_date:new Date().toISOString()};
    ST.notifications=[n,...ST.notifications]; save(); sbUpsert('notifications',n);
  }
  renderReviews(); renderShellNav();
}
function unfeatureReview(id){
  ST.reviews=ST.reviews.map(x=>x.id===id?{...x,front_page:false}:x);
  save(); sbUpsert('reviews',ST.reviews.find(x=>x.id===id)); renderReviews();
}

// ── SETTINGS ─────────────────────────────
function renderSettings(){
  const u=ST.currentUser;
  if(!u) return;
  const admin=isAdmin(u);
  document.getElementById('slot-settings').innerHTML=`
    <div class="topbar"><div style="padding:1rem 1.25rem;display:flex;align-items:center;gap:.75rem">${ic('settings',20,'hsl(221 83% 53%)')} <h1 style="font-size:1.5rem">Settings</h1></div></div>
    <div style="padding:1.25rem;max-width:700px;margin:0 auto;display:flex;flex-direction:column;gap:1.25rem">
      <div id="s-saved"></div>
      <!-- Profile -->
      <div class="card-s" style="border-radius:1rem;overflow:hidden">
        <div style="padding:1.25rem 1.25rem .5rem;display:flex;align-items:center;gap:.5rem">${ic('user',18,'hsl(221 83% 53%)')} <h2 style="font-size:.875rem;font-weight:600">Profile Information</h2></div>
        <div style="padding:.75rem 1.25rem 1.25rem">
          <div style="display:flex;align-items:center;gap:.875rem;margin-bottom:1.25rem">
            <div class="av av-lg">${u.first_name[0]}${u.last_name[0]}</div>
            <div>
              <p style="font-weight:600">${H(u.first_name)} ${H(u.last_name)}</p>
              <p style="font-size:.75rem;color:hsl(var(--muted-fg));display:flex;align-items:center;gap:.25rem">${ic('mail',12)} ${H(u.email)}</p>
              <span class="badge b-role" style="margin-top:.25rem;font-size:.625rem">${admin?'admin':H(u.role)}</span>
            </div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.75rem">
            <div style="display:grid;grid-template-columns:1fr 1fr;gap:.75rem">
              <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">First Name</label><input class="inp" id="sf-first" value="${H(u.first_name)}"/></div>
              <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Last Name</label><input class="inp" id="sf-last" value="${H(u.last_name)}"/></div>
            </div>
            <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Location</label><input class="inp" id="sf-loc" value="${H(u.location||'')}"/></div>
            ${u.role!=='judge'?`<div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">School</label><input class="inp" id="sf-school" value="${H(u.school||'')}"/></div>`:''}
            <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">About You</label><textarea class="inp" id="sf-desc" rows="3" style="resize:vertical">${H(u.description||'')}</textarea></div>
            ${u.role!=='coach'?`<div style="display:flex;align-items:center;justify-content:space-between;background:hsl(var(--muted));border-radius:.75rem;padding:1rem">
              <div><p style="font-weight:500;font-size:.875rem">Available for hire</p><p style="font-size:.75rem;color:hsl(var(--muted-fg))">Let others know you're available</p></div>
              <label class="toggle"><input type="checkbox" id="sf-avail" ${u.available_for_hire?'checked':''}/><span class="toggle-sl"></span></label>
            </div>`:''}
            <button class="btn btn-p" onclick="saveProfile()">${ic('save',16)} Save Changes</button>
          </div>
        </div>
      </div>
      <!-- Account -->
      <div class="card-s" style="border-radius:1rem;overflow:hidden">
        <div style="padding:1.25rem 1.25rem .5rem;display:flex;align-items:center;gap:.5rem">${ic('shield',18,'hsl(221 83% 53%)')} <h2 style="font-size:.875rem;font-weight:600">Account</h2></div>
        <div style="padding:.75rem 1.25rem 1.25rem;display:flex;flex-direction:column;gap:.75rem">
          <div style="display:flex;align-items:center;justify-content:space-between;background:hsl(var(--muted));border-radius:.75rem;padding:1rem">
            <div><p style="font-weight:500;font-size:.875rem">Email Verified</p><p style="font-size:.75rem;color:hsl(var(--muted-fg))">${H(u.email)}</p></div>
            ${ic('check',20,'hsl(134 61% 41%)',2.5)}
          </div>
          <button class="btn btn-d" onclick="doLogout()">${ic('log-out',16)} Log Out</button>
          <button class="btn btn-d" onclick="if(confirm('Delete account? Cannot be undone.')){doLogout()}">${ic('trash-2',16)} Delete Account</button>
        </div>
      </div>
      ${admin?renderAdminPanel():''}
    </div>`;
}

function saveProfile(){
  const u=ST.currentUser;
  if(!u) return;
  const updated={...u,
    first_name:document.getElementById('sf-first')?.value||u.first_name,
    last_name:document.getElementById('sf-last')?.value||u.last_name,
    location:document.getElementById('sf-loc')?.value||u.location,
    school:document.getElementById('sf-school')?.value||u.school,
    description:document.getElementById('sf-desc')?.value||u.description,
    available_for_hire:document.getElementById('sf-avail')?.checked??u.available_for_hire,
  };
  ST.users=ST.users.map(x=>x.id===u.id?updated:x);
  ST.currentUser=updated;
  save(); sbUpsert('profiles',updated);
  const m=document.getElementById('s-saved');
  if(m){m.innerHTML=`<div class="succ">${ic('check',16)} Profile updated successfully!</div>`;setTimeout(()=>{if(m)m.innerHTML=''},3000);}
}

// ── ADMIN PANEL (full role management) ────
function renderAdminPanel(){
  const others=ST.users.filter(u=>u.id!==ST.currentUser?.id);
  const roles=['mentor','judge','coach','teacher','admin'];
  return `<div class="card-s" style="border-radius:1rem;overflow:hidden">
    <div style="padding:1.25rem 1.25rem .5rem;display:flex;align-items:center;gap:.5rem">
      ${ic('shield',18,'hsl(221 83% 53%)')} <h2 style="font-size:.875rem;font-weight:600">Admin — Manage Users</h2>
      <span class="badge" style="margin-left:auto;background:hsl(var(--mc100));color:hsl(var(--mc700))">${others.length} users</span>
    </div>
    <div style="padding:.75rem 1.25rem 1.25rem">
      <div style="display:flex;flex-direction:column;gap:.625rem;max-height:400px;overflow-y:auto">
        ${others.map(u=>`<div style="background:hsl(var(--muted));border-radius:.875rem;padding:.75rem" id="admin-row-${u.id}">
          <div style="display:flex;align-items:center;justify-content:space-between">
            <div style="display:flex;align-items:center;gap:.625rem;min-width:0">
              <div class="av av-xs">${u.first_name[0]}${u.last_name[0]}</div>
              <div style="min-width:0">
                <p style="font-size:.875rem;font-weight:500;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${H(u.first_name)} ${H(u.last_name)}</p>
                <p style="font-size:.75rem;color:hsl(var(--muted-fg));overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${H(u.email)}</p>
              </div>
            </div>
            <div style="display:flex;align-items:center;gap:.375rem;flex-shrink:0">
              <button onclick="toggleRoleMenu('${u.id}')" style="display:flex;align-items:center;gap:.375rem;padding:.375rem .625rem;border-radius:.625rem;font-size:.75rem;font-weight:600;text-transform:capitalize;background:hsl(var(--secondary));color:hsl(var(--secondary-fg))">
                ${H(u.role)} ${ic('chevron-down',12)}
              </button>
              <button onclick="adminDel('${u.id}')" style="padding:.375rem;border-radius:.625rem;color:hsl(var(--destructive));background:hsl(var(--destructive)/.08)">${ic('trash-2',14)}</button>
            </div>
          </div>
          <div id="role-menu-${u.id}" style="display:none;margin-top:.625rem;display:none;flex-wrap:wrap;gap:.375rem;animation:fadeIn .2s ease both">
            ${roles.map(role=>`<button onclick="changeRole('${u.id}','${role}')" style="display:inline-flex;align-items:center;gap:.25rem;padding:.375rem .75rem;border-radius:.625rem;font-size:.75rem;font-weight:500;text-transform:capitalize;cursor:pointer;border:1px solid hsl(var(--border));background:${u.role===role?'hsl(var(--primary))':'hsl(var(--card))'};color:${u.role===role?'#fff':'hsl(var(--fg))'}">
              ${u.role===role?ic('check',12,'#fff'):''} ${role}
            </button>`).join('')}
          </div>
        </div>`).join('')}
      </div>
    </div>
  </div>`;
}

function toggleRoleMenu(uid){
  const m=document.getElementById(`role-menu-${uid}`);
  if(!m) return;
  m.style.display=m.style.display==='none'||m.style.display===''?'flex':'none';
}
function changeRole(uid, role){
  ST.users=ST.users.map(u=>u.id===uid?{...u,role}:u);
  save(); sbUpsert('profiles',ST.users.find(u=>u.id===uid));
  renderSettings();
  showBanner(`Role updated to ${role}`);
}
function adminDel(uid){
  const u=ST.users.find(x=>x.id===uid);
  if(!u) return;
  if(!confirm(`Delete ${u.first_name} ${u.last_name}'s account? Cannot be undone.`)) return;
  ST.users=ST.users.filter(x=>x.id!==uid);
  save(); sbDelete('profiles',uid);
  renderSettings();
}

// ═══════════════════════════════════════════
// AUTH
// ═══════════════════════════════════════════
function doLogin(){
  const email=document.getElementById('li-email')?.value.toLowerCase().trim();
  const pw=document.getElementById('li-pw')?.value;
  const user=ST.users.find(u=>u.email===email&&u.password===pw);
  if(!user){document.getElementById('login-err').innerHTML=`<div class="err">Invalid email or password.</div>`;return;}
  ST.currentUser=user; save();
  goShell('mentors');
}
function doLogout(){
  localStorage.removeItem('mc_user');
  ST.currentUser=null; ST.conversations=[]; ST.notifications=[]; ST.messages={};
  document.getElementById('shell').classList.remove('active');
  document.getElementById('chat-overlay').classList.remove('active');
  showAuth('landing');
}
function toggleEye(inputId,btnId){
  const inp=document.getElementById(inputId);
  const btn=document.getElementById(btnId);
  if(!inp) return;
  const isPass=inp.type==='password';
  inp.type=isPass?'text':'password';
  btn.innerHTML=ic(isPass?'eye-off':'eye',16);
}

// ── SIGNUP ────────────────────────────────
const ROLES_DEF=[
  {id:'mentor',icon:'users',title:'Mentor',desc:'Guide and teach others in debate'},
  {id:'judge',icon:'scale',title:'Judge',desc:'Evaluate debates and competitions'},
  {id:'coach',icon:'book-open',title:'Coach',desc:"Coach your school's team & recruit"},
  {id:'teacher',icon:'graduation-cap',title:'Teacher',desc:'Educate students in schools'},
];
let suStep=1, suRole='', suVerify=false, suCode='';
const suForm={first:'',last:'',email:'',pw:'',country:'United States',state:'',school:''};

function suBack(){
  if(suVerify){suVerify=false;renderSu();return;}
  if(suStep===2){suStep=1;renderSu();return;}
  showAuth('landing');
}
function renderSu(){
  const lb=document.getElementById('su-step-lbl');
  const body=document.getElementById('su-body');
  const backBtn=document.getElementById('su-back-btn');
  if(backBtn) backBtn.innerHTML=`${ic('chevron-left',16)} Back`;
  setIc('signup-logo','sparkles',32,'#fff');

  if(suVerify){
    lb.textContent='Verify your email';
    body.innerHTML=`<p style="font-size:.875rem;color:hsl(var(--muted-fg));margin-bottom:.5rem">Code sent to <strong>${H(suForm.email)}</strong></p>
      <div style="background:hsl(var(--mc50));border:2px dashed hsl(var(--mc300));border-radius:.75rem;padding:1rem;text-align:center;margin-bottom:1.25rem">
        <p style="font-size:.75rem;color:hsl(var(--muted-fg));margin-bottom:.25rem">Verification code</p>
        <p style="font-size:1.875rem;font-family:monospace;font-weight:700;letter-spacing:.3em;color:hsl(var(--primary))">${suCode}</p>
      </div>
      <div id="su-verr"></div>
      <label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Enter 6-digit code</label>
      <input class="inp" id="su-code" maxlength="6" placeholder="000000"
        style="text-align:center;font-size:1.25rem;font-family:monospace;letter-spacing:.3em;margin-bottom:1rem"
        oninput="this.value=this.value.replace(/\\D/g,'').slice(0,6);document.getElementById('su-vbtn').disabled=this.value.length!==6"
        onkeydown="if(event.key==='Enter')doVerify()"/>
      <button class="btn btn-p" id="su-vbtn" onclick="doVerify()" disabled>Verify & Create Account</button>`;
    return;
  }
  if(suStep===1){
    lb.textContent='Step 1 of 2 — Choose your role';
    body.innerHTML=`<div style="display:flex;flex-direction:column;gap:.625rem">
      ${ROLES_DEF.map(r=>`<button onclick="suRole='${r.id}';renderSu()"
        style="display:flex;align-items:center;gap:.875rem;padding:.875rem;border-radius:.75rem;text-align:left;width:100%;
        border:2px solid hsl(var(${suRole===r.id?'--primary':'--border'}));
        background:hsl(var(${suRole===r.id?'--secondary':'--card'}));cursor:pointer">
        <div style="width:40px;height:40px;border-radius:.75rem;display:flex;align-items:center;justify-content:center;flex-shrink:0;background:hsl(var(${suRole===r.id?'--primary':'--muted'}))">
          ${ic(r.icon,20,suRole===r.id?'#fff':'hsl(221 83% 53%)')}
        </div>
        <div style="flex:1;min-width:0">
          <h3 style="font-weight:600;font-size:.875rem">${r.title}</h3>
          <p style="font-size:.75rem;color:hsl(var(--muted-fg))">${r.desc}</p>
        </div>
        ${suRole===r.id?ic('check',20,'hsl(221 83% 53%)'):``}
      </button>`).join('')}
      <button class="btn btn-p" onclick="suStep=2;renderSu()" ${!suRole?'disabled':''} style="margin-top:.5rem">Continue</button>
    </div>
    <p style="text-align:center;font-size:.875rem;color:hsl(var(--muted-fg));margin-top:1rem">Already have an account? <button onclick="showAuth('login')" style="color:hsl(var(--primary));font-weight:600">Log in</button></p>`;
  } else {
    lb.innerHTML=`Step 2 of 2 — Signing up as <strong style="color:hsl(var(--primary));text-transform:capitalize">${suRole}</strong>`;
    body.innerHTML=`<div id="su-err"></div>
      <div style="display:flex;flex-direction:column;gap:.75rem">
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:.75rem">
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">First *</label><input class="inp" id="su2-first" value="${H(suForm.first)}" oninput="suForm.first=this.value"/></div>
          <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Last *</label><input class="inp" id="su2-last" value="${H(suForm.last)}" oninput="suForm.last=this.value"/></div>
        </div>
        <div><label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Email *</label><input type="email" class="inp" id="su2-email" placeholder="you@example.com" value="${H(suForm.email)}" oninput="suForm.email=this.value"/></div>
        <div>
          <label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Password *</label>
          <div style="position:relative">
            <input type="password" class="inp" id="su2-pw" style="padding-right:2.5rem" value="${H(suForm.pw)}" oninput="suForm.pw=this.value"/>
            <button onclick="toggleEye('su2-pw','su2-eye')" style="position:absolute;right:.75rem;top:50%;transform:translateY(-50%)" id="su2-eye">${ic('eye',16)}</button>
          </div>
        </div>
        <div style="position:relative">
          <label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">Country *</label>
          <input class="inp" id="su2-ctry" value="${H(suForm.country)}" placeholder="Select country"
            oninput="suForm.country=this.value;suForm.state='';renderSu()"
            onfocus="showAC('su2-ctry-dd','su2-ctry',COUNTRIES,v=>{suForm.country=v;suForm.state='';renderSu()})"
            onblur="setTimeout(()=>hideAC('su2-ctry-dd'),200)"/>
          <div id="su2-ctry-dd" class="ac-drop" style="display:none"></div>
        </div>
        ${suForm.country==='United States'?`<div style="position:relative">
          <label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">State *</label>
          <input class="inp" id="su2-state" value="${H(suForm.state)}" placeholder="Select state"
            oninput="suForm.state=this.value"
            onfocus="showAC('su2-state-dd','su2-state',US_STATES,v=>{suForm.state=v;document.getElementById('su2-state').value=v})"
            onblur="setTimeout(()=>hideAC('su2-state-dd'),200)"/>
          <div id="su2-state-dd" class="ac-drop" style="display:none"></div>
        </div>`:''}
        <div style="position:relative">
          <label style="display:block;font-size:.875rem;font-weight:500;margin-bottom:.25rem">School (optional)</label>
          <input class="inp" id="su2-school" value="${H(suForm.school)}" placeholder="Search your school…"
            oninput="suForm.school=this.value"
            onfocus="showAC('su2-school-dd','su2-school',SCHOOLS,v=>{suForm.school=v;document.getElementById('su2-school').value=v})"
            onblur="setTimeout(()=>hideAC('su2-school-dd'),200)"/>
          <div id="su2-school-dd" class="ac-drop" style="display:none"></div>
        </div>
        <button class="btn btn-p" onclick="doSignup()">Create Account</button>
      </div>
      <p style="text-align:center;font-size:.875rem;color:hsl(var(--muted-fg));margin-top:1rem">Already have an account? <button onclick="showAuth('login')" style="color:hsl(var(--primary));font-weight:600">Log in</button></p>`;
  }
}
function doSignup(){
  if(!suForm.first||!suForm.last||!suForm.email||!suForm.pw||!suForm.country){setSuErr('Fill in all required fields.');return;}
  if(suForm.country==='United States'&&!suForm.state){setSuErr('Please select your state.');return;}
  if(suForm.pw.length<6){setSuErr('Password must be at least 6 characters.');return;}
  if(ST.users.find(u=>u.email===suForm.email.toLowerCase().trim())){setSuErr('Account already exists.');return;}
  suCode=String(Math.floor(100000+Math.random()*900000));
  suVerify=true; renderSu();
}
function setSuErr(msg){const el=document.getElementById('su-err');if(el)el.innerHTML=`<div class="err">${msg}</div>`}
function doVerify(){
  const inp=document.getElementById('su-code')?.value;
  if(inp!==suCode){document.getElementById('su-verr').innerHTML=`<div class="err">Incorrect code. Try again.</div>`;return;}
  const loc=suForm.country==='United States'?suForm.state:suForm.country;
  const user={id:'u_'+Date.now(),email:suForm.email.toLowerCase().trim(),password:suForm.pw,first_name:suForm.first,last_name:suForm.last,role:suRole,location:loc,school:suForm.school||'',description:'',available_for_hire:suRole!=='coach',tabroom_linked:false,email_verified:true};
  ST.users=[...ST.users,user];
  ST.currentUser=user;
  save(); sbUpsert('profiles',user);
  suStep=1;suRole='';suVerify=false;
  Object.assign(suForm,{first:'',last:'',email:'',pw:'',country:'United States',state:'',school:''});
  goShell('mentors');
}

// ── AUTOCOMPLETE ─────────────────────────
function showAC(ddId,inputId,suggestions,onSelect){
  const dd=document.getElementById(ddId);
  const inp=document.getElementById(inputId);
  if(!dd||!inp) return;
  const q=inp.value.toLowerCase();
  const f=q?suggestions.filter(s=>s.toLowerCase().includes(q)).slice(0,30):suggestions.slice(0,30);
  dd.innerHTML=f.map(s=>`<button class="ac-opt" onmousedown="(${onSelect.toString().replace(/\n/g,' ')})('${H(s).replace(/'/g,"\\'")}');document.getElementById('${ddId}').style.display='none'">${H(s)}</button>`).join('');
  dd.style.display=f.length?'block':'none';
}
function hideAC(ddId){const d=document.getElementById(ddId);if(d)d.style.display='none';}

// ═══════════════════════════════════════════
// UTILS
// ═══════════════════════════════════════════
function H(s){if(s==null)return '';return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;').replace(/'/g,'&#39;')}
function fmtDate(d){try{return new Date(d).toLocaleDateString([],{month:'short',day:'numeric',year:'numeric'})}catch{return d}}

// ═══════════════════════════════════════════
// INIT
// ═══════════════════════════════════════════
async function init(){
  // Setup static icons
  setIc('login-logo','sparkles',32,'#fff');
  setIc('signup-logo','sparkles',32,'#fff');
  setIc('sb-logo','sparkles',16,'#fff');
  setIc('sb-logout-ic','log-out',16);
  setIc('login-back-btn','chevron-left',16);
  document.getElementById('login-back-btn').innerHTML=`${ic('chevron-left',16)} Back`;

  // Try Supabase
  const loaded=await sbLoad();
  if(loaded) showBanner('✓ Connected to Supabase');
  else showBanner('⚠ Running offline — run supabase-schema.sql to enable sync', true);

  // If user was previously logged in, re-verify they exist
  if(ST.currentUser){
    const still=ST.users.find(u=>u.id===ST.currentUser.id);
    if(still){ST.currentUser=still;save();}
    else{ST.currentUser=null;save();}
  }

  document.getElementById('loading').classList.add('hidden');
  initSwipe();

  if(ST.currentUser){
    goShell('mentors');
  } else {
    renderLanding();
    showAuth('landing');
  }
}

init();
</script>
</body>
</html>
