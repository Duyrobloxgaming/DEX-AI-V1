<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DEX AI V1.0</title>
    <style>
        :root {
            --bg-body: #050508;
            --bg-sidebar: rgba(14, 14, 20, 0.65);
            --bg-chat: rgba(7, 7, 12, 0.4);
            --primary: #06b6d4;
            --primary-glow: rgba(6, 182, 212, 0.3);
            --secondary: #818cf8;
            --text-main: #f4f4f5;
            --text-muted: #71717a;
            --border: rgba(39, 39, 42, 0.6);
            --border-glow: rgba(6, 182, 212, 0.15);
            --msg-user: linear-gradient(135deg, #1e1b4b, #27272a);
            --code-bg: #0c0c10;
        }

        body.vip-mode {
            --primary: #f59e0b;
            --primary-glow: rgba(245, 158, 11, 0.4);
            --secondary: #ec4899;
            --border-glow: rgba(245, 158, 11, 0.2);
            background-image: 
                radial-gradient(circle at 10% 20%, rgba(245, 158, 11, 0.08) 0%, transparent 40%),
                radial-gradient(circle at 90% 80%, rgba(236, 72, 153, 0.08) 0%, transparent 40%);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }

        body {
            background-color: var(--bg-body);
            background-image: 
                radial-gradient(circle at 10% 20%, rgba(6, 182, 212, 0.05) 0%, transparent 40%),
                radial-gradient(circle at 90% 80%, rgba(99, 102, 241, 0.05) 0%, transparent 40%);
            color: var(--text-main); height: 100vh; display: flex; overflow: hidden; transition: all 0.3s ease;
        }

        .sidebar {
            width: 300px; background: var(--bg-sidebar); backdrop-filter: blur(20px); -webkit-backdrop-filter: blur(20px);
            border-right: 1px solid var(--border); display: flex; flex-direction: column; padding: 20px 16px; z-index: 5;
        }

        .sidebar-logo { display: flex; align-items: center; gap: 12px; margin-bottom: 24px; padding: 4px 8px; }
        .logo-container { width: 48px; height: 48px; position: relative; display: flex; align-items: center; justify-content: center; filter: drop-shadow(0 0 10px var(--primary-glow)); }
        .logo-svg { width: 100%; height: 100%; overflow: visible; }
        .logo-orbit { transform-box: fill-box; transform-origin: center; animation: logoSpin 14s linear infinite; }
        .logo-orbit-rev { transform-box: fill-box; transform-origin: center; animation: logoSpinRev 22s linear infinite; }
        .logo-core-pulse { transform-box: fill-box; transform-origin: center; animation: logoPulse 2.6s ease-in-out infinite; }
        .logo-node { transform-box: fill-box; transform-origin: center; animation: logoNodeBlink 2.6s ease-in-out infinite; }
        .logo-node:nth-child(2) { animation-delay: 0.5s; }
        .logo-node:nth-child(3) { animation-delay: 1s; }
        @keyframes logoSpin { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
        @keyframes logoSpinRev { from { transform: rotate(360deg); } to { transform: rotate(0deg); } }
        @keyframes logoPulse { 0%, 100% { opacity: 0.55; transform: scale(0.9); } 50% { opacity: 1; transform: scale(1.12); } }
        @keyframes logoNodeBlink { 0%, 100% { opacity: 0.4; } 50% { opacity: 1; } }
        .brand-text { display: flex; flex-direction: column; }
        .brand-name { font-size: 18px; font-weight: 800; letter-spacing: 1px; background: linear-gradient(135deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .brand-sub { font-size: 9px; color: var(--text-muted); font-weight: 700; letter-spacing: 1px; }

        .btn-new-chat {
            background: linear-gradient(135deg, rgba(6, 182, 212, 0.1), rgba(99, 102, 241, 0.1));
            color: var(--text-main); border: 1px solid var(--primary-glow); padding: 12px 14px; border-radius: 12px;
            cursor: pointer; font-weight: 600; font-size: 14px; text-align: left; margin-bottom: 24px;
            display: flex; justify-content: space-between; align-items: center; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }
        .btn-new-chat:hover { border-color: var(--primary); box-shadow: 0 0 15px var(--primary-glow); transform: translateY(-1px); }

        .history-list { flex: 1; overflow-y: auto; display: flex; flex-direction: column; gap: 6px; }
        .history-item {
            padding: 12px; border-radius: 10px; font-size: 13.5px; color: var(--text-muted); cursor: pointer;
            display: flex; justify-content: space-between; align-items: center; border: 1px solid transparent; transition: all 0.25s ease;
        }
        .history-item:hover, .history-item.active { background: rgba(39, 39, 42, 0.4); border-color: var(--border-glow); color: var(--text-main); }
        .history-item.active { border-color: var(--primary-glow); background: linear-gradient(90deg, rgba(6, 182, 212, 0.05), transparent); }
        .history-title-text { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-right: 8px; }
        .history-actions { display: none; gap: 8px; }
        .history-item:hover .history-actions { display: flex; }
        
        .action-icon-btn { background: transparent; border: none; color: var(--text-muted); cursor: pointer; font-size: 13px; transition: color 0.2s; }
        .action-icon-btn:hover { color: var(--primary); }

        .chat-container { flex: 1; display: flex; flex-direction: column; background: var(--bg-chat); backdrop-filter: blur(10px); -webkit-backdrop-filter: blur(10px); }
        .chat-header { padding: 22px 28px; border-bottom: 1px solid var(--border); font-size: 16px; font-weight: 700; letter-spacing: 0.5px; display: flex; justify-content: space-between; align-items: center; background: rgba(10, 10, 15, 0.2); }
        .header-actions { display: flex; align-items: center; gap: 16px; }

        .btn-upgrade {
            background: linear-gradient(135deg, #f59e0b, #ec4899); color: #ffffff; border: none; padding: 8px 16px;
            border-radius: 8px; cursor: pointer; font-size: 13px; font-weight: 800; display: flex; align-items: center; gap: 6px;
            box-shadow: 0 0 10px rgba(245, 158, 11, 0.3); transition: all 0.25s ease;
        }
        .btn-upgrade:hover { transform: scale(1.05); box-shadow: 0 0 15px rgba(245, 158, 11, 0.6); }

        .btn-export-header, .btn-vip-game {
            background: rgba(6, 182, 212, 0.1); color: var(--primary); border: 1px solid rgba(6, 182, 212, 0.3);
            padding: 8px 14px; border-radius: 8px; cursor: pointer; font-size: 13px; font-weight: 700; display: flex; align-items: center; gap: 6px; transition: all 0.25s ease;
        }

        .btn-vip-game {
            background: linear-gradient(135deg, #ec4899, #8b5cf6); color: #ffffff; border: 1px solid rgba(236, 72, 153, 0.5);
            box-shadow: 0 0 12px rgba(236, 72, 153, 0.4); animation: pulseGlow 2s infinite alternate;
        }
        @keyframes pulseGlow { 0% { box-shadow: 0 0 8px rgba(236, 72, 153, 0.4); } 100% { box-shadow: 0 0 18px rgba(236, 72, 153, 0.8); } }

        .btn-export-header:hover, .btn-vip-game:hover { transform: translateY(-2px) scale(1.03); box-shadow: 0 0 15px var(--primary-glow); }

        .chat-body { flex: 1; padding: 30px; overflow-y: auto; scroll-behavior: smooth; display: flex; flex-direction: column; gap: 36px; }
        .message-row { display: flex; flex-direction: column; max-width: 900px; width: 100%; margin: 0 auto; animation: fadeIn 0.4s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        
        .message-row.user { align-items: flex-end; }
        .message-row.ai { align-items: flex-start; }
        .sender-name { font-size: 11px; font-weight: 800; margin-bottom: 8px; color: var(--text-muted); text-transform: uppercase; letter-spacing: 1px; }

        .text-box { font-size: 15px; line-height: 1.7; white-space: pre-wrap; word-break: break-word; width: 100%; }
        .user .text-box { 
            background: var(--msg-user); color: var(--text-main); padding: 14px 22px; border-radius: 18px 18px 2px 18px; 
            width: auto; max-width: 80%; border: 1px solid rgba(255, 255, 255, 0.03); box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
        }
        .ai .text-box { color: #f4f4f5; padding-left: 0; }

        code-block {
            display: block; background-color: var(--code-bg); border: 1px solid rgba(255, 255, 255, 0.04);
            border-left: 3px solid var(--secondary); border-radius: 10px; font-family: 'Consolas', 'Courier New', monospace;
            padding: 16px; margin: 14px 0; overflow-x: auto; font-size: 14px; color: #e4e4e7; box-shadow: inset 0 2px 8px rgba(0,0,0,0.6);
        }

        .action-bar { margin-top: 10px; display: flex; gap: 16px; font-size: 12px; color: var(--text-muted); flex-wrap: wrap; }
        .action-link { cursor: pointer; background: none; border: none; color: var(--text-muted); font-size: 12px; font-weight: 600; transition: all 0.2s; display: flex; align-items: center; gap: 4px; }
        .action-link:hover { color: var(--primary); }
        .action-link.speaking { color: #ef4444; animation: blink 1s infinite; }
        .action-link.edit-btn:hover { color: #f59e0b; }
        .action-link.delete-btn:hover { color: #ef4444; }
        .edit-banner {
            display: none; align-items: center; justify-content: space-between; gap: 12px;
            background: rgba(245, 158, 11, 0.12); border: 1px solid rgba(245, 158, 11, 0.35);
            border-radius: 10px; padding: 10px 14px; margin-bottom: 10px; font-size: 13px; color: #fbbf24; font-weight: 600;
        }
        .edit-banner.visible { display: flex; }
        .edit-banner button {
            background: transparent; border: 1px solid rgba(245, 158, 11, 0.4); color: #fbbf24;
            border-radius: 8px; padding: 4px 10px; cursor: pointer; font-size: 12px; font-weight: 700;
        }
        .edit-banner button:hover { background: rgba(245, 158, 11, 0.2); }
        .message-row.editing-highlight .text-box {
            outline: 2px solid rgba(245, 158, 11, 0.5); box-shadow: 0 0 12px rgba(245, 158, 11, 0.2);
        }
        .msg-edited-tag { font-size: 10px; color: var(--text-muted); font-weight: 600; margin-left: 6px; opacity: 0.8; }
        .img-gallery { display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 12px; margin-top: 14px; }
        .img-gallery.single { grid-template-columns: 1fr; max-width: 560px; }
        .img-card { background: rgba(0,0,0,0.35); border: 1px solid var(--border-glow); border-radius: 14px; overflow: hidden; }
        .img-card img { width: 100%; max-height: 420px; object-fit: contain; display: block; background: #0a0a0f; }
        .img-card .img-meta { padding: 8px 12px; font-size: 11px; color: var(--text-muted); display: flex; justify-content: space-between; gap: 8px; flex-wrap: wrap; }
        .img-card a { color: var(--primary); text-decoration: none; font-weight: 700; }
        .img-card a:hover { text-decoration: underline; }
        .img-style-tags { display: flex; flex-wrap: wrap; gap: 6px; margin: 8px 0 4px; }
        .img-style-tags span { font-size: 11px; padding: 3px 8px; border-radius: 999px; background: rgba(6,182,212,0.12); border: 1px solid rgba(6,182,212,0.25); color: var(--primary); font-weight: 700; }
        .img-loading { padding: 28px 12px; text-align: center; font-size: 13px; color: var(--text-muted); font-weight: 600; }
        .img-card img.img-failed { opacity: 0.35 !important; filter: grayscale(0.5); }
        @keyframes blink { 0%, 100% { opacity: 1; } 50% { opacity: 0.5; } }

        .chat-footer { padding: 30px; max-width: 900px; width: 100%; margin: 0 auto; }
        .file-preview { display: none; background: #0c0c10; padding: 10px 16px; border-radius: 10px; font-size: 12px; border: 1px solid rgba(6, 182, 212, 0.2); margin-bottom: 12px; justify-content: space-between; align-items: center; box-shadow: 0 0 10px rgba(6, 182, 212, 0.05); }

        .input-wrapper { display: flex; background: rgba(20, 20, 25, 0.8); border: 1px solid var(--border); border-radius: 16px; padding: 8px 16px; align-items: center; gap: 12px; transition: all 0.3s; }
        .input-wrapper:focus-within { border-color: var(--primary); box-shadow: 0 0 18px var(--primary-glow); background: rgba(15, 15, 20, 0.95); }
        .input-wrapper input { flex: 1; background: transparent; border: none; outline: none; padding: 12px 4px; color: var(--text-main); font-size: 15.5px; }

        .btn-icon { background: transparent; border: none; color: var(--text-muted); cursor: pointer; font-size: 19px; padding: 6px; display: flex; align-items: center; transition: color 0.2s; }
        .btn-icon:hover { color: var(--primary); }
        
        .btn-submit { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: #050508; border: none; width: 40px; height: 40px; border-radius: 12px; cursor: pointer; font-weight: bold; font-size: 16px; display: flex; align-items: center; justify-content: center; transition: all 0.25s; }
        .btn-submit:hover { transform: scale(1.03); box-shadow: 0 0 12px var(--primary-glow); }
        .btn-submit:disabled { background: #27272a; color: var(--text-muted); cursor: not-allowed; transform: none; box-shadow: none; }

        .vip-game-modal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(3, 3, 6, 0.9); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px); z-index: 999; flex-direction: column; align-items: center; justify-content: center; animation: fadeInModal 0.3s ease-out; }
        @keyframes fadeInModal { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .game-room-container { width: min(1100px, 96vw); max-width: 96%; max-height: 96vh; overflow-y: auto; background: rgba(15, 15, 22, 0.95); border: 1px solid var(--primary); border-radius: 20px; box-shadow: 0 0 40px var(--primary-glow), inset 0 0 15px rgba(255,255,255,0.02); padding: 20px; display: flex; flex-direction: column; align-items: center; position: relative; }
        .close-modal-btn { position: absolute; top: 16px; right: 20px; background: rgba(239, 68, 68, 0.2); color: #ef4444; border: 1px solid rgba(239, 68, 68, 0.4); border-radius: 50%; width: 36px; height: 36px; font-weight: bold; font-size: 18px; cursor: pointer; display: flex; align-items: center; justify-content: center; transition: all 0.2s; }
        .close-modal-btn:hover { background: #ef4444; color: #fff; box-shadow: 0 0 12px rgba(239, 68, 68, 0.6); }
        .game-room-header { text-align: center; margin-bottom: 20px; }
        .game-room-title { font-size: 24px; font-weight: 900; letter-spacing: 2px; background: linear-gradient(135deg, #f59e0b, #ec4899, #818cf8); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .game-tabs { display: flex; gap: 12px; margin-bottom: 16px; background: rgba(0,0,0,0.4); padding: 6px; border-radius: 12px; }
        .tab-btn { background: transparent; color: var(--text-muted); border: none; padding: 10px 24px; border-radius: 8px; font-weight: 700; font-size: 14px; cursor: pointer; transition: all 0.3s; }
        .tab-btn.active { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: #050508; box-shadow: 0 0 12px var(--primary-glow); }
        .game-canvas-wrapper { position: relative; width: 900px; height: 420px; max-width: 100%; background: #000; border-radius: 14px; overflow: hidden; border: 1px solid rgba(255, 255, 255, 0.1); box-shadow: inset 0 0 20px rgba(0,0,0,0.8); }
        canvas { display: block; width: 100%; height: 100%; }
        .game-controls-hint { margin-top: 14px; font-size: 13px; color: var(--text-muted); font-weight: 600; display: flex; gap: 20px; align-items: center; }
        .key-badge { background: rgba(255,255,255,0.1); border: 1px solid rgba(255,255,255,0.2); padding: 2px 8px; border-radius: 4px; color: #fff; font-family: monospace; }

        /* VIP Top-up Modal */
        .vip-topup-modal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(3, 3, 6, 0.92); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px); z-index: 1000; flex-direction: column; align-items: center; justify-content: center; animation: fadeInModal 0.3s ease-out; }
        .topup-container { width: 520px; max-width: 92%; background: rgba(15, 15, 22, 0.97); border: 1px solid rgba(245, 158, 11, 0.5); border-radius: 20px; box-shadow: 0 0 40px rgba(245, 158, 11, 0.25); padding: 28px 24px; position: relative; }
        .topup-title { font-size: 22px; font-weight: 900; text-align: center; background: linear-gradient(135deg, #f59e0b, #ec4899); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 6px; }
        .topup-sub { text-align: center; font-size: 13px; color: var(--text-muted); margin-bottom: 20px; }
        .topup-packages { display: flex; flex-direction: column; gap: 12px; }
        .topup-pkg { background: rgba(255,255,255,0.03); border: 1px solid rgba(255,255,255,0.08); border-radius: 14px; padding: 16px 18px; cursor: pointer; transition: all 0.25s; display: flex; justify-content: space-between; align-items: center; }
        .topup-pkg:hover { border-color: #f59e0b; background: rgba(245, 158, 11, 0.08); transform: translateY(-2px); box-shadow: 0 0 18px rgba(245, 158, 11, 0.2); }
        .topup-pkg.popular { border-color: rgba(236, 72, 153, 0.5); background: linear-gradient(135deg, rgba(245,158,11,0.06), rgba(236,72,153,0.06)); }
        .pkg-info { display: flex; flex-direction: column; gap: 4px; }
        .pkg-name { font-weight: 800; font-size: 15px; color: #fff; }
        .pkg-desc { font-size: 12px; color: var(--text-muted); }
        .pkg-price { font-weight: 900; font-size: 18px; background: linear-gradient(135deg, #f59e0b, #ec4899); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .vip-timer { font-size: 11px; color: #f59e0b; font-family: monospace; font-weight: 700; letter-spacing: 0.3px; }
        .btn-nap-tien { background: linear-gradient(135deg, #f59e0b, #ec4899); color: #fff; border: none; padding: 8px 14px; border-radius: 8px; cursor: pointer; font-size: 13px; font-weight: 800; display: none; align-items: center; gap: 6px; box-shadow: 0 0 10px rgba(245, 158, 11, 0.3); transition: all 0.25s ease; }
        .btn-nap-tien:hover { transform: scale(1.05); box-shadow: 0 0 15px rgba(245, 158, 11, 0.6); }

        /* Modal Cài đặt độ sáng/tối giao diện */
        .settings-modal { display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(3, 3, 6, 0.9); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px); z-index: 1001; flex-direction: column; align-items: center; justify-content: center; animation: fadeInModal 0.3s ease-out; }
        .settings-container { width: 460px; max-width: 92%; background: rgba(15, 15, 22, 0.97); border: 1px solid var(--primary); border-radius: 20px; box-shadow: 0 0 40px var(--primary-glow); padding: 28px 26px; position: relative; }
        .settings-title { font-size: 20px; font-weight: 900; text-align: center; background: linear-gradient(135deg, var(--primary), var(--secondary)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 6px; }
        .settings-sub { text-align: center; font-size: 12.5px; color: var(--text-muted); margin-bottom: 24px; }
        .brightness-row { display: flex; flex-direction: column; gap: 12px; }
        .brightness-label-row { display: flex; justify-content: space-between; align-items: center; font-size: 13px; font-weight: 700; color: var(--tex
