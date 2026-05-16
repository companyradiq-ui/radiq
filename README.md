[radiq.html](https://github.com/user-attachments/files/27838631/radiq.html)
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Radiq — Private Command Hub</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  *{margin:0;padding:0;box-sizing:border-box}
  :root{
    --bg0:#080a0f;--bg1:#0d1018;--bg2:#121520;--bg3:#181d2a;--bg4:#1e2535;
    --neon:#00d4ff;--neon2:#7c3aed;--neon3:#06ffa5;--neon-red:#ff3b5c;
    --text:#e2e8f4;--text2:#8892a4;--text3:#4a5568;
    --border:#1e2a3a;--border2:#2a3a50;
    --glass:rgba(13,16,24,0.7);
    --font:'Space Grotesk',sans-serif;
    --mono:'JetBrains Mono',monospace;
  }
  html{scroll-behavior:smooth}
  body{background:var(--bg0);color:var(--text);font-family:var(--font);overflow-x:hidden}

  /* ─── SCREENS ─── */
  .screen{display:none;min-height:100vh;position:relative}
  .screen.active{display:flex}

  /* ─── PIN SCREEN ─── */
  #pin-screen{
    flex-direction:column;align-items:center;justify-content:center;
    background:radial-gradient(ellipse at 50% 0%, rgba(0,212,255,0.08) 0%, transparent 60%),
               radial-gradient(ellipse at 80% 100%, rgba(124,58,237,0.08) 0%, transparent 60%),
               var(--bg0);
    position:relative;overflow:hidden;
  }
  #pin-screen::before{
    content:'';position:absolute;inset:0;
    background:url("data:image/svg+xml,%3Csvg width='60' height='60' viewBox='0 0 60 60' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='none' fill-rule='evenodd'%3E%3Cg fill='%2300d4ff' fill-opacity='0.03'%3E%3Cpath d='M36 34v-4h-2v4h-4v2h4v4h2v-4h4v-2h-4zm0-30V0h-2v4h-4v2h4v4h2V6h4V4h-4zM6 34v-4H4v4H0v2h4v4h2v-4h4v-2H6zM6 4V0H4v4H0v2h4v4h2V6h4V4H6z'/%3E%3C/g%3E%3C/g%3E%3C/svg%3E");
  }

  /* Scanline effect */
  #pin-screen::after{
    content:'';position:absolute;inset:0;pointer-events:none;
    background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,0.03) 2px,rgba(0,0,0,0.03) 4px);
    animation:scan 8s linear infinite;
  }
  @keyframes scan{from{background-position:0 0}to{background-position:0 100%}}

  .pin-orb{
    position:absolute;border-radius:50%;filter:blur(80px);pointer-events:none;animation:float 6s ease-in-out infinite;
  }
  .pin-orb-1{width:400px;height:400px;background:rgba(0,212,255,0.06);top:-100px;left:-100px;animation-delay:0s}
  .pin-orb-2{width:300px;height:300px;background:rgba(124,58,237,0.08);bottom:-50px;right:-80px;animation-delay:3s}
  @keyframes float{0%,100%{transform:translateY(0)}50%{transform:translateY(-20px)}}

  .pin-container{
    position:relative;z-index:10;
    background:rgba(13,16,24,0.85);
    border:1px solid rgba(0,212,255,0.15);
    border-radius:24px;
    padding:48px 44px;
    width:420px;
    backdrop-filter:blur(20px);
    box-shadow:0 0 60px rgba(0,212,255,0.06),0 40px 80px rgba(0,0,0,0.5);
  }

  .pin-logo{display:flex;align-items:center;gap:12px;justify-content:center;margin-bottom:8px}
  .pin-logo-mark{
    width:44px;height:44px;border-radius:12px;
    background:linear-gradient(135deg,rgba(0,212,255,0.2),rgba(124,58,237,0.2));
    border:1px solid rgba(0,212,255,0.3);
    display:flex;align-items:center;justify-content:center;
    font-size:20px;font-weight:700;color:var(--neon);letter-spacing:-1px;
  }
  .pin-logo-text{font-size:26px;font-weight:700;color:#fff;letter-spacing:-0.5px}
  .pin-logo-text span{color:var(--neon)}

  .pin-badge{
    display:inline-flex;align-items:center;gap:6px;
    background:rgba(0,212,255,0.08);border:1px solid rgba(0,212,255,0.2);
    border-radius:20px;padding:4px 14px;margin:12px auto 28px;
    font-size:11px;font-family:var(--mono);color:var(--neon);letter-spacing:1px;text-transform:uppercase;
  }
  .pin-badge-dot{width:6px;height:6px;border-radius:50%;background:var(--neon);animation:blink 1.5s ease infinite}
  @keyframes blink{0%,100%{opacity:1}50%{opacity:0.3}}

  .pin-title{font-size:16px;font-weight:600;color:var(--text);text-align:center;margin-bottom:4px}
  .pin-sub{font-size:13px;color:var(--text2);text-align:center;margin-bottom:28px}

  /* PIN Dots */
  .pin-dots{display:flex;gap:14px;justify-content:center;margin-bottom:28px}
  .pin-dot{
    width:16px;height:16px;border-radius:50%;
    border:2px solid var(--border2);background:transparent;
    transition:all 0.2s;
  }
  .pin-dot.filled{background:var(--neon);border-color:var(--neon);box-shadow:0 0 10px rgba(0,212,255,0.5)}
  .pin-dot.error{background:var(--neon-red);border-color:var(--neon-red);box-shadow:0 0 10px rgba(255,59,92,0.5)}

  /* Keypad */
  .keypad{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
  .key{
    height:52px;border-radius:12px;border:1px solid var(--border2);
    background:rgba(30,37,53,0.6);color:var(--text);
    font-family:var(--font);font-size:18px;font-weight:600;
    cursor:pointer;transition:all 0.15s;position:relative;overflow:hidden;
    display:flex;align-items:center;justify-content:center;
  }
  .key::before{
    content:'';position:absolute;inset:0;
    background:radial-gradient(circle at center,rgba(0,212,255,0.15),transparent);
    opacity:0;transition:opacity 0.15s;
  }
  .key:hover{border-color:rgba(0,212,255,0.4);background:rgba(0,212,255,0.08);color:#fff;transform:translateY(-1px)}
  .key:hover::before{opacity:1}
  .key:active{transform:scale(0.96)}
  .key.zero{grid-column:2}
  .key.del{font-size:13px;color:var(--text2)}
  .key.del:hover{border-color:rgba(255,59,92,0.4);color:var(--neon-red)}

  .pin-error{
    color:var(--neon-red);font-size:12px;text-align:center;margin-top:14px;
    opacity:0;transition:opacity 0.3s;font-family:var(--mono);letter-spacing:0.5px;
  }
  .pin-error.show{opacity:1}
  .pin-lock{
    display:flex;align-items:center;gap:8px;justify-content:center;
    color:var(--neon-red);font-size:12px;font-family:var(--mono);margin-top:14px;
    opacity:0;transition:opacity 0.3s;
  }
  .pin-lock.show{opacity:1}
  #pin-screen.shake .pin-container{animation:shake 0.4s ease}
  @keyframes shake{0%,100%{transform:translateX(0)}20%{transform:translateX(-8px)}40%{transform:translateX(8px)}60%{transform:translateX(-6px)}80%{transform:translateX(6px)}}
  @keyframes pinSuccess{0%{box-shadow:0 0 0 0 rgba(6,255,165,0)}100%{box-shadow:0 0 0 30px rgba(6,255,165,0)}}
  .pin-container.success{border-color:var(--neon3)!important;box-shadow:0 0 60px rgba(6,255,165,0.2)!important;animation:pinSuccess 0.6s ease}

  /* ─── LANDING SCREEN ─── */
  #landing-screen{
    flex-direction:column;
    background:radial-gradient(ellipse at 50% -20%, rgba(0,212,255,0.1) 0%, transparent 50%),
               var(--bg0);
  }

  /* NAVBAR */
  .navbar{
    position:fixed;top:0;left:0;right:0;z-index:100;
    height:64px;display:flex;align-items:center;justify-content:space-between;
    padding:0 40px;
    background:rgba(8,10,15,0.8);backdrop-filter:blur(20px);
    border-bottom:1px solid rgba(255,255,255,0.05);
  }
  .nav-logo{display:flex;align-items:center;gap:10px;cursor:pointer}
  .nav-logo-mark{
    width:36px;height:36px;border-radius:9px;
    background:linear-gradient(135deg,rgba(0,212,255,0.25),rgba(124,58,237,0.25));
    border:1px solid rgba(0,212,255,0.25);
    display:flex;align-items:center;justify-content:center;
    font-size:15px;font-weight:700;color:var(--neon);
  }
  .nav-logo-text{font-size:20px;font-weight:700;color:#fff;letter-spacing:-0.5px}
  .nav-links{display:flex;gap:28px}
  .nav-link{font-size:14px;color:var(--text2);cursor:pointer;transition:color 0.2s;font-weight:500}
  .nav-link:hover{color:var(--neon)}
  .nav-cta{
    padding:8px 22px;border-radius:8px;
    background:rgba(0,212,255,0.1);border:1px solid rgba(0,212,255,0.3);
    color:var(--neon);font-size:14px;font-weight:600;cursor:pointer;
    transition:all 0.2s;
  }
  .nav-cta:hover{background:rgba(0,212,255,0.2);box-shadow:0 0 20px rgba(0,212,255,0.2)}

  /* HERO */
  .hero{
    flex:1;display:flex;flex-direction:column;align-items:center;justify-content:center;
    padding:100px 40px 60px;text-align:center;position:relative;overflow:hidden;
  }
  .hero-particles{position:absolute;inset:0;pointer-events:none}
  .particle{
    position:absolute;width:2px;height:2px;border-radius:50%;background:var(--neon);
    animation:drift linear infinite;opacity:0;
  }
  @keyframes drift{
    0%{transform:translateY(100vh);opacity:0}
    10%{opacity:0.6}
    90%{opacity:0.6}
    100%{transform:translateY(-20px);opacity:0}
  }

  .hero-badge{
    display:inline-flex;align-items:center;gap:8px;
    background:rgba(0,212,255,0.06);border:1px solid rgba(0,212,255,0.15);
    border-radius:20px;padding:6px 16px;margin-bottom:28px;
    font-size:12px;font-family:var(--mono);color:var(--neon);letter-spacing:1px;text-transform:uppercase;
    animation:fadeUp 0.8s ease both;
  }
  .hero-title{
    font-size:clamp(44px,6vw,80px);font-weight:700;line-height:1.1;
    letter-spacing:-2px;margin-bottom:20px;
    animation:fadeUp 0.8s ease 0.1s both;
  }
  .hero-title .accent{
    background:linear-gradient(135deg,var(--neon),var(--neon2));
    -webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text;
  }
  .hero-sub{
    font-size:18px;color:var(--text2);max-width:560px;line-height:1.7;margin-bottom:40px;
    animation:fadeUp 0.8s ease 0.2s both;
  }
  .hero-actions{display:flex;gap:14px;animation:fadeUp 0.8s ease 0.3s both}
  .btn-primary{
    padding:13px 32px;border-radius:10px;
    background:linear-gradient(135deg,rgba(0,212,255,0.2),rgba(124,58,237,0.2));
    border:1px solid rgba(0,212,255,0.4);
    color:#fff;font-size:15px;font-weight:600;cursor:pointer;
    transition:all 0.25s;letter-spacing:-0.2px;
  }
  .btn-primary:hover{
    background:linear-gradient(135deg,rgba(0,212,255,0.35),rgba(124,58,237,0.35));
    box-shadow:0 0 30px rgba(0,212,255,0.25),0 8px 32px rgba(0,0,0,0.3);
    transform:translateY(-2px);
  }
  .btn-secondary{
    padding:13px 32px;border-radius:10px;
    background:transparent;border:1px solid var(--border2);
    color:var(--text2);font-size:15px;font-weight:600;cursor:pointer;transition:all 0.25s;
  }
  .btn-secondary:hover{border-color:rgba(0,212,255,0.3);color:var(--text);transform:translateY(-2px)}

  .hero-preview{
    margin-top:60px;width:100%;max-width:900px;
    border-radius:16px;border:1px solid var(--border);
    overflow:hidden;position:relative;
    box-shadow:0 40px 80px rgba(0,0,0,0.6),0 0 60px rgba(0,212,255,0.05);
    animation:fadeUp 0.8s ease 0.4s both;
  }
  .hero-preview-bar{
    height:36px;background:var(--bg2);border-bottom:1px solid var(--border);
    display:flex;align-items:center;padding:0 14px;gap:8px;
  }
  .bar-dot{width:10px;height:10px;border-radius:50%}
  .bar-dot:nth-child(1){background:#ff5f57}
  .bar-dot:nth-child(2){background:#ffbd2e}
  .bar-dot:nth-child(3){background:#28c840}
  .preview-content{
    background:var(--bg1);height:260px;display:flex;
    font-size:13px;color:var(--text2);font-family:var(--mono);
    padding:20px;align-items:flex-start;flex-direction:column;gap:6px;
    overflow:hidden;
  }
  .preview-line{display:flex;gap:10px;align-items:center}
  .preview-line .prompt{color:var(--neon3);font-size:12px}
  .preview-line .cmd{color:var(--text)}
  .preview-line .tag{
    padding:1px 8px;border-radius:4px;font-size:11px;
    background:rgba(0,212,255,0.1);color:var(--neon);border:1px solid rgba(0,212,255,0.15);
  }
  .preview-line .tag.green{background:rgba(6,255,165,0.1);color:var(--neon3);border-color:rgba(6,255,165,0.15)}
  .preview-line .tag.purple{background:rgba(124,58,237,0.15);color:#a78bfa;border-color:rgba(124,58,237,0.2)}

  @keyframes fadeUp{from{opacity:0;transform:translateY(24px)}to{opacity:1;transform:translateY(0)}}

  /* FEATURES */
  .features{
    padding:80px 40px;max-width:1100px;margin:0 auto;width:100%;
  }
  .section-label{
    font-size:12px;font-family:var(--mono);color:var(--neon);letter-spacing:2px;text-transform:uppercase;
    text-align:center;margin-bottom:12px;
  }
  .section-title{font-size:36px;font-weight:700;text-align:center;letter-spacing:-1px;margin-bottom:12px}
  .section-sub{font-size:16px;color:var(--text2);text-align:center;margin-bottom:48px}
  .feature-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:16px}
  .feature-card{
    background:var(--bg2);border:1px solid var(--border);border-radius:16px;
    padding:28px 24px;transition:all 0.3s;cursor:default;
  }
  .feature-card:hover{border-color:rgba(0,212,255,0.25);background:var(--bg3);transform:translateY(-3px);box-shadow:0 20px 40px rgba(0,0,0,0.3)}
  .feature-icon{
    width:44px;height:44px;border-radius:10px;margin-bottom:16px;
    display:flex;align-items:center;justify-content:center;font-size:20px;
  }
  .feature-icon.blue{background:rgba(0,212,255,0.1);border:1px solid rgba(0,212,255,0.2);color:var(--neon)}
  .feature-icon.purple{background:rgba(124,58,237,0.1);border:1px solid rgba(124,58,237,0.2);color:#a78bfa}
  .feature-icon.green{background:rgba(6,255,165,0.1);border:1px solid rgba(6,255,165,0.2);color:var(--neon3)}
  .feature-icon.red{background:rgba(255,59,92,0.1);border:1px solid rgba(255,59,92,0.2);color:var(--neon-red)}
  .feature-title{font-size:16px;font-weight:600;margin-bottom:8px}
  .feature-desc{font-size:14px;color:var(--text2);line-height:1.6}

  /* FOOTER */
  .footer{
    border-top:1px solid var(--border);padding:24px 40px;
    display:flex;align-items:center;justify-content:space-between;
    font-size:13px;color:var(--text3);max-width:100%;
  }

  /* ─── AUTH SCREEN ─── */
  #auth-screen{
    flex-direction:column;align-items:center;justify-content:center;
    background:radial-gradient(ellipse at 30% 50%, rgba(124,58,237,0.07) 0%, transparent 50%),
               radial-gradient(ellipse at 70% 50%, rgba(0,212,255,0.07) 0%, transparent 50%),
               var(--bg0);
  }
  .auth-container{
    background:rgba(13,16,24,0.9);border:1px solid var(--border2);border-radius:20px;
    padding:40px;width:440px;backdrop-filter:blur(20px);
    box-shadow:0 40px 80px rgba(0,0,0,0.5);
  }
  .auth-header{text-align:center;margin-bottom:32px}
  .auth-tabs{display:flex;gap:0;background:var(--bg3);border-radius:10px;padding:4px;margin-bottom:28px}
  .auth-tab{
    flex:1;padding:9px;text-align:center;font-size:14px;font-weight:600;
    cursor:pointer;border-radius:7px;transition:all 0.2s;color:var(--text2);
  }
  .auth-tab.active{background:var(--bg4);color:var(--text);box-shadow:0 1px 4px rgba(0,0,0,0.3)}
  .form-group{margin-bottom:16px}
  .form-label{font-size:12px;font-weight:500;color:var(--text2);text-transform:uppercase;letter-spacing:0.5px;margin-bottom:6px;display:block}
  .form-input{
    width:100%;padding:11px 14px;border-radius:9px;
    background:var(--bg3);border:1px solid var(--border2);
    color:var(--text);font-size:14px;font-family:var(--font);
    transition:all 0.2s;outline:none;
  }
  .form-input:focus{border-color:rgba(0,212,255,0.4);box-shadow:0 0 0 3px rgba(0,212,255,0.08)}
  .form-input::placeholder{color:var(--text3)}
  .form-submit{
    width:100%;padding:12px;border-radius:9px;margin-top:8px;
    background:linear-gradient(135deg,rgba(0,212,255,0.2),rgba(124,58,237,0.2));
    border:1px solid rgba(0,212,255,0.35);color:#fff;
    font-size:15px;font-weight:600;cursor:pointer;font-family:var(--font);
    transition:all 0.2s;
  }
  .form-submit:hover{background:linear-gradient(135deg,rgba(0,212,255,0.35),rgba(124,58,237,0.35));transform:translateY(-1px);box-shadow:0 8px 20px rgba(0,212,255,0.15)}
  .auth-switch{text-align:center;margin-top:16px;font-size:13px;color:var(--text2)}
  .auth-link{color:var(--neon);cursor:pointer;font-weight:500}
  .auth-back{
    position:absolute;top:20px;left:20px;display:flex;align-items:center;gap:6px;
    color:var(--text2);font-size:13px;cursor:pointer;transition:color 0.2s;padding:8px 12px;
    border-radius:8px;
  }
  .auth-back:hover{color:var(--text);background:var(--bg2)}

  /* ─── APP SCREEN ─── */
  #app-screen{
    flex-direction:row;
    background:var(--bg0);
    height:100vh;overflow:hidden;
  }

  /* SERVER SIDEBAR */
  .server-sidebar{
    width:68px;background:var(--bg0);border-right:1px solid var(--border);
    display:flex;flex-direction:column;align-items:center;padding:12px 0;gap:8px;
    flex-shrink:0;overflow-y:auto;
  }
  .server-icon{
    width:44px;height:44px;border-radius:14px;cursor:pointer;
    display:flex;align-items:center;justify-content:center;
    font-size:15px;font-weight:700;transition:all 0.2s;
    background:var(--bg2);border:1px solid var(--border);color:var(--text2);
    position:relative;
  }
  .server-icon:hover{border-radius:12px;background:var(--bg3);color:var(--text)}
  .server-icon.active{
    border-radius:12px;background:rgba(0,212,255,0.15);
    border-color:rgba(0,212,255,0.35);color:var(--neon);
    box-shadow:0 0 15px rgba(0,212,255,0.15);
  }
  .server-icon.active::before{
    content:'';position:absolute;left:-5px;top:50%;transform:translateY(-50%);
    width:4px;height:20px;background:var(--neon);border-radius:0 2px 2px 0;
  }
  .server-divider{width:32px;height:1px;background:var(--border);margin:4px 0;flex-shrink:0}
  .server-add{
    width:44px;height:44px;border-radius:50%;cursor:pointer;
    display:flex;align-items:center;justify-content:center;
    font-size:20px;color:var(--neon3);
    background:rgba(6,255,165,0.06);border:1px dashed rgba(6,255,165,0.2);
    transition:all 0.2s;
  }
  .server-add:hover{border-radius:14px;background:rgba(6,255,165,0.12)}

  /* CHANNEL SIDEBAR */
  .channel-sidebar{
    width:240px;background:var(--bg1);border-right:1px solid var(--border);
    display:flex;flex-direction:column;flex-shrink:0;
  }
  .channel-header{
    padding:0 16px;height:52px;border-bottom:1px solid var(--border);
    display:flex;align-items:center;justify-content:space-between;
    font-size:15px;font-weight:700;cursor:pointer;
  }
  .channel-header:hover{background:var(--bg2)}
  .channel-section-header{
    padding:16px 16px 6px;font-size:11px;font-weight:700;
    color:var(--text3);text-transform:uppercase;letter-spacing:0.8px;
    display:flex;align-items:center;justify-content:space-between;
    cursor:pointer;
  }
  .channel-section-header:hover{color:var(--text2)}
  .channel-item{
    padding:5px 10px 5px 14px;margin:0 8px;border-radius:7px;
    display:flex;align-items:center;gap:8px;cursor:pointer;
    font-size:14px;color:var(--text2);transition:all 0.15s;
  }
  .channel-item:hover{background:var(--bg3);color:var(--text)}
  .channel-item.active{background:var(--bg4);color:var(--text)}
  .channel-hash{font-size:16px;font-weight:700;color:var(--text3);line-height:1}
  .channel-item.active .channel-hash{color:var(--text2)}
  .channel-unread{
    margin-left:auto;background:var(--neon-red);color:#fff;
    font-size:10px;font-weight:700;padding:1px 5px;border-radius:10px;min-width:18px;text-align:center;
  }

  /* USER INFO */
  .user-info{
    margin-top:auto;padding:10px 12px;border-top:1px solid var(--border);
    display:flex;align-items:center;gap:10px;background:var(--bg1);
  }
  .avatar{
    width:32px;height:32px;border-radius:50%;
    background:linear-gradient(135deg,var(--neon2),var(--neon));
    display:flex;align-items:center;justify-content:center;
    font-size:13px;font-weight:700;color:#fff;flex-shrink:0;position:relative;cursor:pointer;
  }
  .avatar-status{
    position:absolute;bottom:-1px;right:-1px;width:10px;height:10px;
    border-radius:50%;background:var(--neon3);border:2px solid var(--bg1);
  }
  .avatar-status.away{background:#f59e0b}
  .avatar-status.offline{background:var(--text3)}
  .user-info-text{flex:1;min-width:0}
  .user-info-name{font-size:13px;font-weight:600;color:var(--text);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
  .user-info-status{font-size:11px;color:var(--text3);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
  .user-info-actions{display:flex;gap:4px}
  .icon-btn{
    width:28px;height:28px;border-radius:6px;display:flex;align-items:center;justify-content:center;
    color:var(--text2);cursor:pointer;transition:all 0.15s;font-size:14px;background:transparent;border:none;
  }
  .icon-btn:hover{background:var(--bg3);color:var(--text)}

  /* MAIN CHAT */
  .chat-main{flex:1;display:flex;flex-direction:column;min-width:0}
  .chat-header{
    height:52px;border-bottom:1px solid var(--border);
    display:flex;align-items:center;padding:0 16px;gap:10px;flex-shrink:0;
    background:var(--bg1);
  }
  .chat-header-channel{font-size:15px;font-weight:700;display:flex;align-items:center;gap:8px}
  .chat-header-desc{font-size:13px;color:var(--text3);margin-left:8px;padding-left:8px;border-left:1px solid var(--border)}
  .chat-header-actions{margin-left:auto;display:flex;gap:4px}

  /* MESSAGES */
  .messages-area{flex:1;overflow-y:auto;padding:16px 16px 0;display:flex;flex-direction:column;gap:0}
  .messages-area::-webkit-scrollbar{width:4px}
  .messages-area::-webkit-scrollbar-track{background:transparent}
  .messages-area::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px}

  .day-divider{
    display:flex;align-items:center;gap:12px;margin:20px 0;
    font-size:12px;font-weight:600;color:var(--text3);
  }
  .day-divider::before,.day-divider::after{content:'';flex:1;height:1px;background:var(--border)}

  .message{
    display:flex;gap:14px;padding:4px 4px;border-radius:8px;
    transition:background 0.15s;
  }
  .message:hover{background:rgba(255,255,255,0.02)}
  .message.consecutive{padding-top:1px;padding-bottom:1px}
  .message.consecutive .msg-avatar{visibility:hidden;height:0;margin-top:2px}
  .msg-avatar{
    width:36px;height:36px;border-radius:50%;flex-shrink:0;margin-top:2px;
    display:flex;align-items:center;justify-content:center;font-size:14px;font-weight:700;color:#fff;
  }
  .msg-content{flex:1;min-width:0}
  .msg-header{display:flex;align-items:baseline;gap:8px;margin-bottom:3px}
  .msg-author{font-size:14px;font-weight:600;color:var(--text);cursor:pointer}
  .msg-author:hover{text-decoration:underline}
  .msg-role{
    font-size:10px;font-weight:700;padding:1px 6px;border-radius:4px;
    color:#fff;letter-spacing:0.3px;
  }
  .msg-time{font-size:11px;color:var(--text3)}
  .msg-text{font-size:14px;color:var(--text);line-height:1.6;word-break:break-word}
  .msg-text code{
    font-family:var(--mono);font-size:13px;background:var(--bg3);
    padding:1px 5px;border-radius:4px;border:1px solid var(--border);
  }

  /* TYPING INDICATOR */
  .typing-indicator{
    padding:8px 16px 4px;font-size:12px;color:var(--text3);display:flex;align-items:center;gap:8px;flex-shrink:0;
  }
  .typing-dots{display:flex;gap:3px;align-items:center}
  .typing-dot{width:5px;height:5px;border-radius:50%;background:var(--text3);animation:typingAnim 1.4s ease infinite}
  .typing-dot:nth-child(2){animation-delay:0.2s}
  .typing-dot:nth-child(3){animation-delay:0.4s}
  @keyframes typingAnim{0%,80%,100%{transform:scale(0.8);opacity:0.5}40%{transform:scale(1);opacity:1}}

  /* MESSAGE INPUT */
  .input-area{padding:12px 16px 16px;flex-shrink:0}
  .input-box{
    background:var(--bg3);border:1px solid var(--border2);border-radius:10px;
    display:flex;align-items:center;gap:8px;padding:0 12px;
    transition:border-color 0.2s;
  }
  .input-box:focus-within{border-color:rgba(0,212,255,0.3)}
  .input-field{
    flex:1;background:transparent;border:none;outline:none;
    color:var(--text);font-size:14px;font-family:var(--font);padding:12px 0;
    resize:none;max-height:120px;
  }
  .input-field::placeholder{color:var(--text3)}
  .input-actions{display:flex;gap:4px;align-items:center}
  .input-send{
    width:32px;height:32px;border-radius:7px;border:none;cursor:pointer;
    background:rgba(0,212,255,0.15);color:var(--neon);
    display:flex;align-items:center;justify-content:center;font-size:14px;
    transition:all 0.2s;
  }
  .input-send:hover{background:rgba(0,212,255,0.25);transform:scale(1.05)}
  .input-send:active{transform:scale(0.95)}

  /* MEMBERS SIDEBAR */
  .members-sidebar{
    width:220px;background:var(--bg1);border-left:1px solid var(--border);
    padding:16px 0;overflow-y:auto;flex-shrink:0;
  }
  .members-sidebar::-webkit-scrollbar{width:4px}
  .members-sidebar::-webkit-scrollbar-thumb{background:var(--border);border-radius:2px}
  .members-section{margin-bottom:24px}
  .members-section-header{
    padding:0 16px 8px;font-size:11px;font-weight:700;
    color:var(--text3);text-transform:uppercase;letter-spacing:0.8px;
  }
  .member-item{
    padding:5px 10px;margin:0 6px;border-radius:7px;
    display:flex;align-items:center;gap:10px;cursor:pointer;
    transition:background 0.15s;
  }
  .member-item:hover{background:var(--bg3)}
  .member-name{font-size:14px;font-weight:500;color:var(--text2)}
  .member-item:hover .member-name{color:var(--text)}
  .member-status-text{font-size:11px;color:var(--text3);white-space:nowrap;overflow:hidden;text-overflow:ellipsis}

  /* ROLE COLORS */
  .role-owner{background:#ff3b5c;color:#fff}
  .role-ceo{background:#7c3aed;color:#fff}
  .role-manager{background:#0891b2;color:#fff}
  .role-developer{background:#0284c7;color:#fff}
  .role-designer{background:#7c3aed;color:#fff}
  .role-moderator{background:#059669;color:#fff}
  .role-member{background:#374151;color:#9ca3af}
  .role-bot{background:#22c55e;color:#fff}

  .av-owner{background:linear-gradient(135deg,#ff3b5c,#ff6b35)}
  .av-ceo{background:linear-gradient(135deg,#7c3aed,#a855f7)}
  .av-manager{background:linear-gradient(135deg,#0891b2,#06b6d4)}
  .av-dev{background:linear-gradient(135deg,#2563eb,#3b82f6)}
  .av-designer{background:linear-gradient(135deg,#7c3aed,#ec4899)}
  .av-member{background:linear-gradient(135deg,#374151,#6b7280)}

  /* SYSTEM MSG */
  .system-msg{
    display:flex;align-items:center;gap:10px;padding:8px 4px;
    font-size:13px;color:var(--text3);
  }
  .system-msg-icon{
    width:36px;height:36px;border-radius:50%;flex-shrink:0;
    background:rgba(0,212,255,0.08);border:1px solid rgba(0,212,255,0.15);
    display:flex;align-items:center;justify-content:center;font-size:15px;
  }

  /* NOTIFICATION */
  .notif-dot{
    position:absolute;top:-3px;right:-3px;width:14px;height:14px;
    border-radius:50%;background:var(--neon-red);border:2px solid var(--bg0);
    display:flex;align-items:center;justify-content:center;
    font-size:8px;font-weight:700;color:#fff;
  }

  /* MODAL OVERLAY */
  .modal-overlay{
    display:none;position:fixed;inset:0;z-index:1000;
    background:rgba(0,0,0,0.7);backdrop-filter:blur(4px);
    align-items:center;justify-content:center;
  }
  .modal-overlay.show{display:flex}
  .modal{
    background:var(--bg2);border:1px solid var(--border2);border-radius:16px;
    padding:28px;width:460px;max-width:95vw;
    box-shadow:0 40px 80px rgba(0,0,0,0.5);
  }
  .modal-title{font-size:18px;font-weight:700;margin-bottom:16px}
  .modal-close{
    float:right;background:none;border:none;color:var(--text2);font-size:20px;
    cursor:pointer;padding:0;line-height:1;
  }
  .modal-close:hover{color:var(--text)}

  /* TOAST */
  .toast-container{position:fixed;bottom:20px;right:20px;z-index:2000;display:flex;flex-direction:column;gap:8px}
  .toast{
    background:var(--bg3);border:1px solid var(--border2);border-radius:10px;
    padding:12px 16px;display:flex;align-items:center;gap:10px;
    font-size:13px;min-width:260px;box-shadow:0 8px 24px rgba(0,0,0,0.3);
    animation:toastIn 0.3s ease;
  }
  @keyframes toastIn{from{opacity:0;transform:translateX(20px)}to{opacity:1;transform:translateX(0)}}
  .toast-icon{font-size:16px}
  .toast.success .toast-icon{color:var(--neon3)}
  .toast.error .toast-icon{color:var(--neon-red)}
  .toast.info .toast-icon{color:var(--neon)}

  /* SEARCH BAR */
  .search-bar{
    width:200px;padding:6px 12px;border-radius:6px;
    background:var(--bg0);border:1px solid var(--border);
    color:var(--text);font-size:13px;font-family:var(--font);outline:none;
    transition:all 0.2s;
  }
  .search-bar:focus{border-color:rgba(0,212,255,0.3);width:240px}
  .search-bar::placeholder{color:var(--text3)}

  /* SCROLLBAR GLOBAL */
  ::-webkit-scrollbar{width:5px;height:5px}
  ::-webkit-scrollbar-track{background:transparent}
  ::-webkit-scrollbar-thumb{background:var(--border2);border-radius:3px}

  /* RESPONSIVE */
  @media(max-width:768px){
    .members-sidebar{display:none}
    .channel-sidebar{width:200px}
    .navbar{padding:0 20px}
    .nav-links{display:none}
    .hero{padding:80px 20px 40px}
    .feature-grid{grid-template-columns:1fr}
    .features{padding:60px 20px}
  }
  @media(max-width:500px){
    .channel-sidebar{display:none}
    .server-sidebar{width:58px}
  }

  /* ADMIN BADGE */
  .admin-crown{
    position:absolute;top:-8px;left:50%;transform:translateX(-50%);
    font-size:12px;
  }

  /* PING ANIMATION */
  @keyframes pingAnim{0%{box-shadow:0 0 0 0 rgba(0,212,255,0.4)}100%{box-shadow:0 0 0 8px rgba(0,212,255,0)}}

  /* Loading overlay */
  #loading-overlay{
    position:fixed;inset:0;z-index:9999;
    background:var(--bg0);display:flex;flex-direction:column;align-items:center;justify-content:center;gap:20px;
    transition:opacity 0.5s;
  }
  #loading-overlay.hide{opacity:0;pointer-events:none}
  .loading-logo{font-size:32px;font-weight:700;color:#fff;letter-spacing:-1px}
  .loading-logo span{color:var(--neon)}
  .loading-bar{width:200px;height:2px;background:var(--border2);border-radius:1px;overflow:hidden}
  .loading-bar-inner{height:100%;background:linear-gradient(90deg,var(--neon2),var(--neon));animation:loadBar 1.5s ease both}
  @keyframes loadBar{from{width:0}to{width:100%}}
  .loading-text{font-size:12px;font-family:var(--mono);color:var(--text3);letter-spacing:1px}
</style>
</head>
<body>

<!-- LOADING OVERLAY -->
<div id="loading-overlay">
  <div class="loading-logo">radi<span>q</span></div>
  <div class="loading-bar"><div class="loading-bar-inner"></div></div>
  <div class="loading-text">INITIALIZING SECURE CONNECTION...</div>
</div>

<!-- TOAST CONTAINER -->
<div class="toast-container" id="toastContainer"></div>

<!-- ═══════════════════════════════════════════
     SCREEN 1: PIN GATEWAY
═══════════════════════════════════════════ -->
<div class="screen" id="pin-screen">
  <div class="pin-orb pin-orb-1"></div>
  <div class="pin-orb pin-orb-2"></div>
  <div class="pin-container" id="pinContainer">
    <div class="pin-logo">
      <div class="pin-logo-mark">rq</div>
      <div class="pin-logo-text">radi<span>q</span></div>
    </div>
    <div style="text-align:center">
      <div class="pin-badge"><div class="pin-badge-dot"></div>SECURE ACCESS REQUIRED</div>
    </div>
    <div class="pin-title">Enter Security PIN</div>
    <div class="pin-sub">This platform is restricted. Enter your PIN to continue.</div>
    <div class="pin-dots" id="pinDots">
      <div class="pin-dot"></div>
      <div class="pin-dot"></div>
      <div class="pin-dot"></div>
      <div class="pin-dot"></div>
    </div>
    <div class="keypad">
      <button class="key" onclick="pinPress('1')">1</button>
      <button class="key" onclick="pinPress('2')">2</button>
      <button class="key" onclick="pinPress('3')">3</button>
      <button class="key" onclick="pinPress('4')">4</button>
      <button class="key" onclick="pinPress('5')">5</button>
      <button class="key" onclick="pinPress('6')">6</button>
      <button class="key" onclick="pinPress('7')">7</button>
      <button class="key" onclick="pinPress('8')">8</button>
      <button class="key" onclick="pinPress('9')">9</button>
      <button class="key zero" onclick="pinPress('0')">0</button>
      <button class="key del" onclick="pinDelete()">⌫ DEL</button>
    </div>
    <div class="pin-error" id="pinError">⚠ Incorrect PIN — Access Denied</div>
    <div class="pin-lock" id="pinLock">🔒 Too many attempts. Wait <span id="lockTimer">30</span>s</div>
  </div>
</div>

<!-- ═══════════════════════════════════════════
     SCREEN 2: LANDING PAGE
═══════════════════════════════════════════ -->
<div class="screen" id="landing-screen">
  <nav class="navbar">
    <div class="nav-logo" onclick="showScreen('landing-screen')">
      <div class="nav-logo-mark">rq</div>
      <div class="nav-logo-text">radiq</div>
    </div>
    <div class="nav-links">
      <span class="nav-link">Features</span>
      <span class="nav-link">Security</span>
      <span class="nav-link">Team</span>
      <span class="nav-link">Docs</span>
    </div>
    <button class="nav-cta" onclick="showScreen('auth-screen')">Secure Access →</button>
  </nav>
  <div style="flex:1;display:flex;flex-direction:column;overflow-y:auto">
    <div class="hero">
      <div class="hero-particles" id="particles"></div>
      <div class="hero-badge"><div class="pin-badge-dot"></div>Private Command Hub — v2.4.1</div>
      <h1 class="hero-title">Where <span class="accent">Radiq</span><br>Operates</h1>
      <p class="hero-sub">A private, encrypted communication and management platform built exclusively for the Radiq team. Real-time collaboration, role-based access, and military-grade security.</p>
      <div class="hero-actions">
        <button class="btn-primary" onclick="showScreen('auth-screen')">⚡ Enter Platform</button>
        <button class="btn-secondary">View Features ↓</button>
      </div>
      <div class="hero-preview">
        <div class="hero-preview-bar">
          <div class="bar-dot"></div><div class="bar-dot"></div><div class="bar-dot"></div>
          <span style="font-size:12px;color:var(--text3);margin-left:auto;font-family:var(--mono)">radiq.hub — #general</span>
        </div>
        <div class="preview-content">
          <div class="preview-line"><span class="prompt">▶</span><span class="cmd">Connected to Radiq Hub</span><span class="tag green">SECURE</span></div>
          <div class="preview-line"><span class="prompt">▶</span><span class="cmd">Authenticating session...</span><span class="tag">JWT ✓</span></div>
          <div class="preview-line"><span class="prompt">▶</span><span class="cmd">Loading channels...</span><span class="tag">8 rooms</span></div>
          <div class="preview-line"><span class="prompt">▶</span><span class="cmd">Permissions resolved</span><span class="tag purple">OWNER</span></div>
          <div class="preview-line" style="margin-top:12px"><span style="color:var(--text3);font-size:11px">iyadlia50</span><span style="color:var(--text3);font-size:11px;margin-left:8px">Today at 9:41 AM</span></div>
          <div class="preview-line"><span class="cmd" style="color:var(--text)">Welcome to the Radiq command hub. All systems operational. 🚀</span></div>
        </div>
      </div>
    </div>
    <div class="features">
      <div class="section-label">Platform Features</div>
      <h2 class="section-title">Everything your team needs</h2>
      <p class="section-sub">Built for professionals who need more than just chat.</p>
      <div class="feature-grid">
        <div class="feature-card">
          <div class="feature-icon blue">🔐</div>
          <div class="feature-title">Military-Grade Security</div>
          <div class="feature-desc">PIN gateway, JWT auth, rate limiting, and encrypted sessions. Only authorized members can access.</div>
        </div>
        <div class="feature-card">
          <div class="feature-icon purple">⚡</div>
          <div class="feature-title">Real-Time Messaging</div>
          <div class="feature-desc">WebSocket-powered instant messaging with typing indicators, reactions, and file sharing.</div>
        </div>
        <div class="feature-card">
          <div class="feature-icon green">🎭</div>
          <div class="feature-title">Role Hierarchy</div>
          <div class="feature-desc">Owner, CEO, Manager, Developer, Designer, Moderator — each with granular permissions.</div>
        </div>
        <div class="feature-card">
          <div class="feature-icon blue">📡</div>
          <div class="feature-title">Company Channels</div>
          <div class="feature-desc">#general, #dev, #design, #announcements and more — organize your team's workflow.</div>
        </div>
        <div class="feature-card">
          <div class="feature-icon red">🛡️</div>
          <div class="feature-title">Admin Dashboard</div>
          <div class="feature-desc">Full control over users, permissions, channels, logs, and security settings.</div>
        </div>
        <div class="feature-card">
          <div class="feature-icon purple">📊</div>
          <div class="feature-title">Activity Logs</div>
          <div class="feature-desc">Track every login, message, PIN attempt, and permission change in real-time.</div>
        </div>
      </div>
    </div>
    <footer class="footer">
      <div style="display:flex;align-items:center;gap:8px"><div class="nav-logo-mark" style="width:24px;height:24px;font-size:11px;border-radius:6px">rq</div><span>radiq</span></div>
      <span>Private Platform — Authorized Access Only</span>
      <span>© 2025 Radiq</span>
    </footer>
  </div>
</div>

<!-- ═══════════════════════════════════════════
     SCREEN 3: AUTH (LOGIN/REGISTER)
═══════════════════════════════════════════ -->
<div class="screen" id="auth-screen">
  <span class="auth-back" onclick="showScreen('landing-screen')">← Back</span>
  <div class="auth-container">
    <div class="auth-header">
      <div class="pin-logo" style="justify-content:center;margin-bottom:4px">
        <div class="pin-logo-mark">rq</div>
        <div class="pin-logo-text">radi<span>q</span></div>
      </div>
      <div style="font-size:14px;color:var(--text3);margin-top:4px">Authorized Personnel Only</div>
    </div>
    <div class="auth-tabs">
      <div class="auth-tab active" id="loginTab" onclick="switchAuthTab('login')">Sign In</div>
      <div class="auth-tab" id="registerTab" onclick="switchAuthTab('register')">Register</div>
    </div>
    <div id="loginForm">
      <div class="form-group">
        <label class="form-label">Email Address</label>
        <input type="email" class="form-input" id="loginEmail" placeholder="your@email.com">
      </div>
      <div class="form-group">
        <label class="form-label">Password</label>
        <input type="password" class="form-input" id="loginPassword" placeholder="••••••••">
      </div>
      <button class="form-submit" onclick="handleLogin()">Sign In to Radiq →</button>
    </div>
    <div id="registerForm" style="display:none">
      <div class="form-group">
        <label class="form-label">Username</label>
        <input type="text" class="form-input" id="regUsername" placeholder="Your username">
      </div>
      <div class="form-group">
        <label class="form-label">Email Address</label>
        <input type="email" class="form-input" id="regEmail" placeholder="your@email.com">
      </div>
      <div class="form-group">
        <label class="form-label">Password</label>
        <input type="password" class="form-input" id="regPassword" placeholder="Min. 8 characters">
      </div>
      <div class="form-group">
        <label class="form-label">Confirm Password</label>
        <input type="password" class="form-input" id="regConfirm" placeholder="Repeat password">
      </div>
      <button class="form-submit" onclick="handleRegister()">Create Account →</button>
    </div>
    <div class="auth-switch" id="authSwitch">
      Don't have an account? <span class="auth-link" onclick="switchAuthTab('register')">Register</span>
    </div>
  </div>
</div>

<!-- ═══════════════════════════════════════════
     SCREEN 4: MAIN APP
═══════════════════════════════════════════ -->
<div class="screen" id="app-screen">
  <!-- Server Sidebar -->
  <div class="server-sidebar">
    <div class="server-icon active" title="Radiq HQ" style="position:relative">
      <div class="admin-crown" id="ownerCrown" style="display:none">👑</div>
      rq
    </div>
    <div class="server-divider"></div>
    <div class="server-icon" title="Development" style="font-size:13px">DEV</div>
    <div class="server-icon" title="Design Lab" style="font-size:13px">DSN</div>
    <div class="server-icon" title="Operations" style="font-size:12px">OPS</div>
    <div class="server-divider"></div>
    <div class="server-add" title="Add Server">+</div>
  </div>

  <!-- Channel Sidebar -->
  <div class="channel-sidebar">
    <div class="channel-header">
      <span>Radiq HQ</span>
      <span style="font-size:18px;color:var(--text3)">⌄</span>
    </div>
    <div style="overflow-y:auto;flex:1;padding:8px 0">
      <div class="channel-section-header">Text Channels</div>
      <div class="channel-item active" onclick="switchChannel('general','#general','Company-wide announcements')">
        <span class="channel-hash">#</span><span>general</span>
        <span class="channel-unread" style="display:none">3</span>
      </div>
      <div class="channel-item" onclick="switchChannel('announcements','#announcements','Official announcements')">
        <span class="channel-hash">#</span><span>announcements</span>
      </div>
      <div class="channel-item" onclick="switchChannel('development','#development','Dev team discussion')">
        <span class="channel-hash">#</span><span>development</span>
      </div>
      <div class="channel-item" onclick="switchChannel('design','#design','Design team')">
        <span class="channel-hash">#</span><span>design</span>
      </div>
      <div class="channel-item" onclick="switchChannel('staff','#staff','Staff only')">
        <span class="channel-hash">#</span><span>staff</span>
      </div>
      <div class="channel-item" onclick="switchChannel('media','#media','Share media')">
        <span class="channel-hash">#</span><span>media</span>
      </div>
      <div class="channel-item" onclick="switchChannel('ideas','#ideas','Share ideas')">
        <span class="channel-hash">#</span><span>ideas</span>
      </div>
      <div class="channel-item" onclick="switchChannel('support','#support','Get help')">
        <span class="channel-hash">#</span><span>support</span>
        <span class="channel-unread">2</span>
      </div>
      <div class="channel-section-header" style="margin-top:8px">Voice Channels</div>
      <div class="channel-item"><span style="font-size:16px;color:var(--text3)">🔊</span><span>Team Voice</span></div>
      <div class="channel-item"><span style="font-size:16px;color:var(--text3)">🔊</span><span>Dev Room</span></div>
    </div>
    <div class="user-info">
      <div class="avatar av-owner" id="sidebarAvatar" style="font-size:13px">
        <span id="sidebarAvatarText">U</span>
        <div class="avatar-status"></div>
      </div>
      <div class="user-info-text">
        <div class="user-info-name" id="sidebarUsername">User</div>
        <div class="user-info-status" id="sidebarStatus">Online</div>
      </div>
      <div class="user-info-actions">
        <button class="icon-btn" title="Settings" onclick="showToast('Settings coming soon','info')">⚙</button>
        <button class="icon-btn" title="Logout" onclick="handleLogout()">⏏</button>
      </div>
    </div>
  </div>

  <!-- Main Chat -->
  <div class="chat-main">
    <div class="chat-header">
      <div class="chat-header-channel">
        <span style="font-size:18px;font-weight:700;color:var(--text3)">#</span>
        <span id="currentChannel">general</span>
        <span class="chat-header-desc" id="currentChannelDesc">Company-wide announcements</span>
      </div>
      <div class="chat-header-actions">
        <button class="icon-btn" title="Search" onclick="showToast('Search coming soon','info')">🔍</button>
        <button class="icon-btn" title="Members" onclick="toggleMembers()">👥</button>
        <button class="icon-btn" title="Notifications">🔔</button>
        <button class="icon-btn" title="Pin messages">📌</button>
        <input class="search-bar" placeholder="Search messages...">
      </div>
    </div>
    <div class="messages-area" id="messagesArea">
      <!-- Messages injected here -->
    </div>
    <div class="typing-indicator" id="typingIndicator" style="display:none">
      <div class="typing-dots">
        <div class="typing-dot"></div>
        <div class="typing-dot"></div>
        <div class="typing-dot"></div>
      </div>
      <span id="typingText">Someone is typing...</span>
    </div>
    <div class="input-area">
      <div class="input-box">
        <button class="icon-btn" title="Attach file" style="font-size:16px">📎</button>
        <textarea class="input-field" id="messageInput" rows="1" placeholder="Message #general" onkeydown="handleInputKey(event)" oninput="handleTyping()"></textarea>
        <div class="input-actions">
          <button class="icon-btn" title="Emoji" style="font-size:16px">😊</button>
          <button class="icon-btn" title="GIF" style="font-size:12px;font-weight:700;color:var(--text2)">GIF</button>
          <button class="input-send" onclick="sendMessage()" title="Send">➤</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Members Sidebar -->
  <div class="members-sidebar" id="membersSidebar">
    <div class="members-section">
      <div class="members-section-header">Online — <span id="onlineCount">5</span></div>
      <div id="onlineMembers"></div>
    </div>
    <div class="members-section">
      <div class="members-section-header">Offline — 3</div>
      <div id="offlineMembers"></div>
    </div>
  </div>
</div>

<script>
// ══════════════════════════════════════════════
//  STATE
// ══════════════════════════════════════════════
const CORRECT_PIN = '1982';
const ADMIN_EMAIL = 'iyadlia50@gmail.com';
let pinInput = '';
let failedAttempts = 0;
let pinLocked = false;
let lockInterval = null;
let currentUser = null;
let membersVisible = true;
let typingTimer = null;
let currentChannelName = 'general';

// Mock users DB
const users = [
  {id:1,username:'iyadlia50',email:ADMIN_EMAIL,password:'admin1982',role:'owner',color:'av-owner',roleLabel:'Owner'},
  {id:2,username:'Alex_CEO',email:'alex@radiq.io',password:'pass123',role:'ceo',color:'av-ceo',roleLabel:'CEO'},
  {id:3,username:'dev_sarah',email:'sarah@radiq.io',password:'pass123',role:'developer',color:'av-dev',roleLabel:'Developer'},
  {id:4,username:'mark_design',email:'mark@radiq.io',password:'pass123',role:'designer',color:'av-designer',roleLabel:'Designer'},
];

// ══════════════════════════════════════════════
//  INIT
// ══════════════════════════════════════════════
window.onload = function() {
  // Generate particles
  const p = document.getElementById('particles');
  for(let i=0;i<30;i++){
    const d = document.createElement('div');
    d.className='particle';
    d.style.left=Math.random()*100+'%';
    d.style.animationDuration=(8+Math.random()*12)+'s';
    d.style.animationDelay=(Math.random()*10)+'s';
    d.style.opacity=0.3+Math.random()*0.4;
    p.appendChild(d);
  }
  // Loading screen
  setTimeout(()=>{
    document.getElementById('loading-overlay').classList.add('hide');
    setTimeout(()=>{
      document.getElementById('loading-overlay').style.display='none';
      showScreen('pin-screen');
    },500);
  },1800);
};

// ══════════════════════════════════════════════
//  NAVIGATION
// ══════════════════════════════════════════════
function showScreen(id){
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.getElementById(id).classList.add('active');
}

// ══════════════════════════════════════════════
//  PIN SYSTEM
// ══════════════════════════════════════════════
function pinPress(digit){
  if(pinLocked||pinInput.length>=4) return;
  pinInput+=digit;
  updatePinDots();
  if(pinInput.length===4) setTimeout(checkPin,200);
}
function pinDelete(){
  if(pinLocked) return;
  pinInput=pinInput.slice(0,-1);
  updatePinDots();
  hidePinError();
}
function updatePinDots(){
  const dots=document.querySelectorAll('.pin-dot');
  dots.forEach((d,i)=>{
    d.classList.toggle('filled',i<pinInput.length);
    d.classList.remove('error');
  });
}
function checkPin(){
  if(pinInput===CORRECT_PIN){
    document.querySelectorAll('.pin-dot').forEach(d=>{d.classList.add('filled');d.style.background='var(--neon3)';d.style.borderColor='var(--neon3)';d.style.boxShadow='0 0 10px rgba(6,255,165,0.5)'});
    document.getElementById('pinContainer').classList.add('success');
    showToast('Access granted — Welcome to Radiq','info');
    setTimeout(()=>{showScreen('landing-screen');resetPin();},1200);
  } else {
    failedAttempts++;
    document.querySelectorAll('.pin-dot').forEach(d=>{d.classList.remove('filled');d.classList.add('error')});
    document.getElementById('pin-screen').classList.add('shake');
    setTimeout(()=>document.getElementById('pin-screen').classList.remove('shake'),400);
    if(failedAttempts>=4){
      pinLocked=true;
      let t=30;
      document.getElementById('lockTimer').textContent=t;
      document.getElementById('pinError').classList.remove('show');
      document.getElementById('pinLock').classList.add('show');
      lockInterval=setInterval(()=>{
        t--;document.getElementById('lockTimer').textContent=t;
        if(t<=0){clearInterval(lockInterval);pinLocked=false;failedAttempts=0;document.getElementById('pinLock').classList.remove('show');resetPin();}
      },1000);
    } else {
      const el=document.getElementById('pinError');
      el.textContent=`⚠ Incorrect PIN — ${4-failedAttempts} attempts left`;
      el.classList.add('show');
      setTimeout(()=>{resetPin();},600);
    }
  }
}
function resetPin(){pinInput='';updatePinDots();}
function hidePinError(){document.getElementById('pinError').classList.remove('show');}

// ══════════════════════════════════════════════
//  AUTH
// ══════════════════════════════════════════════
function switchAuthTab(tab){
  document.getElementById('loginTab').classList.toggle('active',tab==='login');
  document.getElementById('registerTab').classList.toggle('active',tab==='register');
  document.getElementById('loginForm').style.display=tab==='login'?'block':'none';
  document.getElementById('registerForm').style.display=tab==='register'?'block':'none';
  document.getElementById('authSwitch').innerHTML=tab==='login'
    ?`Don't have an account? <span class="auth-link" onclick="switchAuthTab('register')">Register</span>`
    :`Already have an account? <span class="auth-link" onclick="switchAuthTab('login')">Sign In</span>`;
}
function handleLogin(){
  const email=document.getElementById('loginEmail').value.trim();
  const pass=document.getElementById('loginPassword').value;
  if(!email||!pass){showToast('Please fill all fields','error');return;}
  const user=users.find(u=>u.email===email&&u.password===pass);
  if(user){
    currentUser=user;
    enterApp();
  } else {
    showToast('Invalid email or password','error');
  }
}
function handleRegister(){
  const un=document.getElementById('regUsername').value.trim();
  const email=document.getElementById('regEmail').value.trim();
  const pass=document.getElementById('regPassword').value;
  const conf=document.getElementById('regConfirm').value;
  if(!un||!email||!pass||!conf){showToast('Please fill all fields','error');return;}
  if(pass!==conf){showToast('Passwords do not match','error');return;}
  if(pass.length<6){showToast('Password too short','error');return;}
  if(users.find(u=>u.email===email)){showToast('Email already registered','error');return;}
  const isAdmin=email===ADMIN_EMAIL;
  const newUser={
    id:users.length+1,username:un,email,password:pass,
    role:isAdmin?'owner':'member',
    color:isAdmin?'av-owner':'av-member',
    roleLabel:isAdmin?'Owner':'Member'
  };
  users.push(newUser);
  currentUser=newUser;
  showToast('Account created! Welcome to Radiq 🚀','info');
  setTimeout(enterApp,800);
}
function handleLogout(){
  currentUser=null;
  showScreen('pin-screen');
  resetPin();
  showToast('Logged out successfully','info');
}
function enterApp(){
  if(!currentUser) return;
  document.getElementById('sidebarUsername').textContent=currentUser.username;
  document.getElementById('sidebarAvatarText').textContent=currentUser.username[0].toUpperCase();
  document.getElementById('sidebarAvatar').className='avatar '+currentUser.color;
  if(currentUser.role==='owner') document.getElementById('ownerCrown').style.display='block';
  loadMessages('general');
  renderMembers();
  showScreen('app-screen');
  showToast(`Welcome back, ${currentUser.username}! 👋`,'info');
}

// ══════════════════════════════════════════════
//  CHANNELS
// ══════════════════════════════════════════════
function switchChannel(name,label,desc){
  currentChannelName=name;
  document.getElementById('currentChannel').textContent=name;
  document.getElementById('currentChannelDesc').textContent=desc;
  document.querySelectorAll('.channel-item').forEach(el=>el.classList.remove('active'));
  event.currentTarget.classList.add('active');
  const inp=document.getElementById('messageInput');
  inp.placeholder=`Message ${label}`;
  loadMessages(name);
}

// ══════════════════════════════════════════════
//  MESSAGES
// ══════════════════════════════════════════════
const channelMessages = {
  general:[
    {author:'iyadlia50',role:'owner',roleLabel:'Owner',color:'av-owner',time:'Today at 9:41 AM',text:'Welcome to the Radiq command hub! 🚀 All systems are operational and the platform is live.',id:1},
    {author:'Alex_CEO',role:'ceo',roleLabel:'CEO',color:'av-ceo',time:'Today at 9:45 AM',text:'Great work getting this up and running. Team — check the #announcements channel for today\'s briefing.',id:2},
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev',time:'Today at 10:02 AM',text:'Backend is looking solid. WebSocket connections are stable, latency under 20ms. 🔥',id:3},
    {author:'mark_design',role:'designer',roleLabel:'Designer',color:'av-designer',time:'Today at 10:15 AM',text:'The UI is coming together nicely. Dropped the new mockups in <code>#design</code> — please review when you get a chance.',id:4},
  ],
  announcements:[
    {type:'system',text:'This channel is for official announcements only. Only admins can post here.'},
    {author:'iyadlia50',role:'owner',roleLabel:'Owner',color:'av-owner',time:'Today at 9:00 AM',text:'📢 Team meeting scheduled for Friday 3:00 PM. All hands required. Agenda will be shared in #staff.',id:5},
    {author:'Alex_CEO',role:'ceo',roleLabel:'CEO',color:'av-ceo',time:'Today at 9:30 AM',text:'🎉 Q2 targets exceeded by 23%! Outstanding performance from all departments. Full report coming end of week.',id:6},
  ],
  development:[
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev',time:'Yesterday at 4:20 PM',text:'Sprint 12 planning doc is in Notion. We\'re tackling the auth system refactor and API rate limiting this cycle.',id:7},
    {author:'iyadlia50',role:'owner',roleLabel:'Owner',color:'av-owner',time:'Yesterday at 4:45 PM',text:'Approved. Also add WebSocket reconnection logic to the backlog — we need graceful failover.',id:8},
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev',time:'Today at 8:30 AM',text:'PR #47 is ready for review — added JWT refresh token rotation and session invalidation. Looking for 2 approvals.',id:9},
  ],
  design:[
    {author:'mark_design',role:'designer',roleLabel:'Designer',color:'av-designer',time:'Yesterday at 2:10 PM',text:'New brand kit uploaded. Dark mode variants are finalized. Tokens exported to Figma variables.',id:10},
    {author:'mark_design',role:'designer',roleLabel:'Designer',color:'av-designer',time:'Today at 10:15 AM',text:'Dashboard v3 mockups are live in Figma. The glassmorphism treatment is dialed in — less heavy, more refined.',id:11},
  ],
  staff:[
    {author:'Alex_CEO',role:'ceo',roleLabel:'CEO',color:'av-ceo',time:'Today at 9:35 AM',text:'Reminder: performance reviews are due by EOW. HR forms are in the shared drive.',id:12},
    {author:'iyadlia50',role:'owner',roleLabel:'Owner',color:'av-owner',time:'Today at 9:50 AM',text:'New hire onboarding for the two ML engineers starts Monday. Please be available for intro calls.',id:13},
  ],
  ideas:[
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev',time:'Today at 11:00 AM',text:'Idea: AI-assisted code review integration in the dev channel. Could flag potential bugs automatically. Thoughts?',id:14},
    {author:'mark_design',role:'designer',roleLabel:'Designer',color:'av-designer',time:'Today at 11:08 AM',text:'Love it. Also been thinking about a status page that auto-updates from our monitoring. Full transparency with clients.',id:15},
  ],
  support:[
    {type:'system',text:'Need help? Post your question here and a team member will assist.'},
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev',time:'Today at 12:00 PM',text:'If you\'re having issues with the 2FA setup, check the docs at docs.radiq.io — full walkthrough there.',id:16},
  ],
  media:[
    {type:'system',text:'Share screenshots, videos, and media assets here.'},
  ]
};

function loadMessages(channel){
  const area=document.getElementById('messagesArea');
  area.innerHTML='';
  const msgs=channelMessages[channel]||[];
  // Welcome banner
  const banner=document.createElement('div');
  banner.innerHTML=`<div style="padding:24px 4px 16px;border-bottom:1px solid var(--border);margin-bottom:16px">
    <div style="font-size:32px;margin-bottom:8px">#</div>
    <div style="font-size:20px;font-weight:700;margin-bottom:4px">Welcome to #${channel}!</div>
    <div style="font-size:14px;color:var(--text2)">This is the start of the #${channel} channel.</div>
  </div>`;
  area.appendChild(banner);
  if(msgs.length===0){area.innerHTML+='<div style="padding:20px 4px;font-size:14px;color:var(--text3)">No messages yet. Be the first!</div>';}
  msgs.forEach((msg,i)=>{
    if(msg.type==='system'){
      const el=document.createElement('div');
      el.className='system-msg';
      el.innerHTML=`<div class="system-msg-icon">🤖</div><span>${msg.text}</span>`;
      area.appendChild(el);
      return;
    }
    const el=document.createElement('div');
    el.className='message';
    el.setAttribute('data-id',msg.id);
    el.innerHTML=`
      <div class="msg-avatar ${msg.color}">${msg.author[0].toUpperCase()}</div>
      <div class="msg-content">
        <div class="msg-header">
          <span class="msg-author">${msg.author}</span>
          <span class="msg-role role-${msg.role}">${msg.roleLabel}</span>
          <span class="msg-time">${msg.time}</span>
        </div>
        <div class="msg-text">${msg.text}</div>
        <div class="msg-reactions" id="reactions-${msg.id}" style="display:flex;gap:6px;margin-top:6px;flex-wrap:wrap"></div>
      </div>
      <div class="msg-actions" style="margin-left:auto;display:none;gap:4px;align-items:center">
        <button class="icon-btn" onclick="addReaction(${msg.id})" title="React" style="font-size:14px">😊</button>
        <button class="icon-btn" onclick="replyMsg(${msg.id})" title="Reply" style="font-size:12px">↩</button>
      </div>
    `;
    el.addEventListener('mouseenter',()=>el.querySelector('.msg-actions').style.display='flex');
    el.addEventListener('mouseleave',()=>el.querySelector('.msg-actions').style.display='none');
    area.appendChild(el);
  });
  area.scrollTop=area.scrollHeight;
}

function sendMessage(){
  const input=document.getElementById('messageInput');
  const text=input.value.trim();
  if(!text||!currentUser) return;
  const msg={
    author:currentUser.username,role:currentUser.role,
    roleLabel:currentUser.roleLabel,color:currentUser.color,
    time:'Today at '+new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}),
    text:text,id:Date.now()
  };
  if(!channelMessages[currentChannelName]) channelMessages[currentChannelName]=[];
  channelMessages[currentChannelName].push(msg);
  input.value='';
  input.style.height='auto';
  appendMessage(msg);
  simulateReply();
}
function appendMessage(msg){
  const area=document.getElementById('messagesArea');
  const el=document.createElement('div');
  el.className='message';
  el.setAttribute('data-id',msg.id);
  el.innerHTML=`
    <div class="msg-avatar ${msg.color}">${msg.author[0].toUpperCase()}</div>
    <div class="msg-content">
      <div class="msg-header">
        <span class="msg-author">${msg.author}</span>
        <span class="msg-role role-${msg.role}">${msg.roleLabel}</span>
        <span class="msg-time">${msg.time}</span>
      </div>
      <div class="msg-text">${msg.text}</div>
      <div class="msg-reactions" id="reactions-${msg.id}" style="display:flex;gap:6px;margin-top:6px;flex-wrap:wrap"></div>
    </div>
  `;
  area.appendChild(el);
  area.scrollTop=area.scrollHeight;
}
function addReaction(id){
  const emojis=['👍','❤️','🔥','✅','😂','🚀'];
  const e=emojis[Math.floor(Math.random()*emojis.length)];
  const container=document.getElementById('reactions-'+id);
  if(!container) return;
  const existing=Array.from(container.children).find(c=>c.textContent.startsWith(e));
  if(existing){
    const count=parseInt(existing.getAttribute('data-count')||'1')+1;
    existing.setAttribute('data-count',count);
    existing.textContent=e+' '+count;
  } else {
    const badge=document.createElement('div');
    badge.setAttribute('data-count','1');
    badge.textContent=e+' 1';
    badge.style.cssText='background:var(--bg3);border:1px solid var(--border2);border-radius:6px;padding:2px 8px;font-size:13px;cursor:pointer;transition:background 0.2s';
    badge.addEventListener('mouseenter',()=>badge.style.background='var(--bg4)');
    container.appendChild(badge);
  }
}
function replyMsg(id){showToast('Reply feature — tap message to quote','info');}
function simulateReply(){
  if(currentChannelName!=='general') return;
  const responses=['On it! 👊','Noted ✅','Thanks for the update!','Will check shortly.','Solid work 🔥'];
  const bots=[
    {author:'Radiq Bot',role:'bot',roleLabel:'Bot',color:'av-dev'},
    {author:'dev_sarah',role:'developer',roleLabel:'Developer',color:'av-dev'},
  ];
  if(Math.random()>0.5) return;
  setTimeout(()=>{
    const ti=document.getElementById('typingIndicator');
    const bot=bots[Math.floor(Math.random()*bots.length)];
    ti.style.display='flex';
    document.getElementById('typingText').textContent=`${bot.author} is typing...`;
    setTimeout(()=>{
      ti.style.display='none';
      const msg={
        author:bot.author,role:bot.role,roleLabel:bot.roleLabel,color:bot.color,
        time:'Today at '+new Date().toLocaleTimeString([],{hour:'2-digit',minute:'2-digit'}),
        text:responses[Math.floor(Math.random()*responses.length)],id:Date.now()
      };
      appendMessage(msg);
    },2000);
  },1000);
}

// ══════════════════════════════════════════════
//  MEMBERS SIDEBAR
// ══════════════════════════════════════════════
const onlineUsers=[
  {username:'iyadlia50',role:'owner',color:'av-owner',status:'Online',roleLabel:'Owner'},
  {username:'Alex_CEO',role:'ceo',color:'av-ceo',status:'In Meeting',roleLabel:'CEO'},
  {username:'dev_sarah',role:'developer',color:'av-dev',status:'Coding...',roleLabel:'Developer'},
  {username:'mark_design',role:'designer',color:'av-designer',status:'In Figma',roleLabel:'Designer'},
  {username:'Radiq Bot',role:'bot',color:'av-dev',status:'Always online',roleLabel:'Bot'},
];
const offlineUsers=[
  {username:'mod_jake',role:'moderator',color:'av-member',roleLabel:'Moderator'},
  {username:'member_01',role:'member',color:'av-member',roleLabel:'Member'},
  {username:'member_02',role:'member',color:'av-member',roleLabel:'Member'},
];
function renderMembers(){
  const onlineEl=document.getElementById('onlineMembers');
  const offlineEl=document.getElementById('offlineMembers');
  onlineEl.innerHTML='';
  offlineEl.innerHTML='';
  onlineUsers.forEach(m=>{
    onlineEl.innerHTML+=`
      <div class="member-item">
        <div class="avatar ${m.color}" style="width:32px;height:32px;font-size:12px;font-weight:700;position:relative">
          ${m.username[0].toUpperCase()}
          <div class="avatar-status"></div>
        </div>
        <div>
          <div class="member-name">${m.username}</div>
          <div class="member-status-text">${m.status||''}</div>
        </div>
        <span class="msg-role role-${m.role}" style="margin-left:auto;font-size:9px">${m.roleLabel}</span>
      </div>`;
  });
  offlineUsers.forEach(m=>{
    offlineEl.innerHTML+=`
      <div class="member-item" style="opacity:0.5">
        <div class="avatar av-member" style="width:32px;height:32px;font-size:12px;font-weight:700;position:relative">
          ${m.username[0].toUpperCase()}
          <div class="avatar-status offline"></div>
        </div>
        <div>
          <div class="member-name">${m.username}</div>
          <div class="member-status-text">Offline</div>
        </div>
      </div>`;
  });
}
function toggleMembers(){
  const ms=document.getElementById('membersSidebar');
  membersVisible=!membersVisible;
  ms.style.display=membersVisible?'block':'none';
}

// ══════════════════════════════════════════════
//  INPUT HELPERS
// ══════════════════════════════════════════════
function handleInputKey(e){
  if(e.key==='Enter'&&!e.shiftKey){e.preventDefault();sendMessage();}
  const ta=e.target;ta.style.height='auto';ta.style.height=Math.min(ta.scrollHeight,120)+'px';
}
function handleTyping(){
  const ta=document.getElementById('messageInput');
  ta.style.height='auto';ta.style.height=Math.min(ta.scrollHeight,120)+'px';
}

// ══════════════════════════════════════════════
//  TOAST NOTIFICATIONS
// ══════════════════════════════════════════════
function showToast(msg,type='info'){
  const tc=document.getElementById('toastContainer');
  const icons={info:'💬',error:'⚠️',success:'✅'};
  const t=document.createElement('div');
  t.className=`toast ${type}`;
  t.innerHTML=`<span class="toast-icon">${icons[type]||'💬'}</span><span>${msg}</span>`;
  tc.appendChild(t);
  setTimeout(()=>{t.style.opacity='0';t.style.transform='translateX(20px)';t.style.transition='all 0.3s';setTimeout(()=>t.remove(),300);},3500);
}
</script>
</body>
</html>
