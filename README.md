<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>THE BRASS WORKSHOP — Ronaldo Ribeiro</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link href="https://fonts.googleapis.com/css2?family=Special+Elite&family=Share+Tech+Mono&family=Cinzel:wght@400;700;900&family=Orbitron:wght@400;700;900&display=swap" rel="stylesheet" />
  <style>
    :root {
      --coal: #0a0704;
      --coal-mid: #150e08;
      --coal-light: #1f1510;
      --copper: #b5520a;
      --copper-light: #d4691a;
      --brass: #c8950a;
      --brass-light: #e8b520;
      --amber: #ff8c00;
      --amber-glow: #ffaa33;
      --amber-pale: #ffc87a;
      --green-ox: #3a5c28;
      --green-terminal: #00ff41;
      --green-term-dim: #00cc33;
      --brown: #5c3a1e;
      --rust: #8b3010;
      --steel: #4a4540;
      --steam: rgba(200,180,150,0.15);
      --glow-copper: 0 0 20px rgba(181,82,10,0.6), 0 0 40px rgba(181,82,10,0.3);
      --glow-amber: 0 0 20px rgba(255,140,0,0.7), 0 0 50px rgba(255,140,0,0.3);
      --glow-green: 0 0 10px rgba(0,255,65,0.8), 0 0 30px rgba(0,255,65,0.3);
      --font-mono: 'Share Tech Mono', monospace;
      --font-display: 'Cinzel', serif;
      --font-type: 'Special Elite', cursive;
      --font-tech: 'Orbitron', monospace;
    }

    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    html { scroll-behavior: smooth; }

    body {
      background: var(--coal);
      color: var(--amber-pale);
      font-family: var(--font-mono);
      overflow-x: hidden;
      cursor: crosshair;
    }

    ::selection { background: var(--copper); color: var(--coal); }

    /* ─── SCROLLBAR ─────────────────────────────────────────── */
    ::-webkit-scrollbar { width: 8px; }
    ::-webkit-scrollbar-track { background: var(--coal); }
    ::-webkit-scrollbar-thumb { background: var(--copper); border-radius: 4px; }
    ::-webkit-scrollbar-thumb:hover { background: var(--brass); }

    /* ─── CRT OVERLAY ───────────────────────────────────────── */
    .crt-overlay {
      position: fixed; inset: 0; z-index: 9999;
      pointer-events: none;
      background: repeating-linear-gradient(
        0deg,
        transparent,
        transparent 2px,
        rgba(0,0,0,0.08) 2px,
        rgba(0,0,0,0.08) 4px
      );
      animation: crt-flicker 8s infinite;
    }
    @keyframes crt-flicker {
      0%,95%,100% { opacity: 1; }
      96% { opacity: 0.97; }
      97% { opacity: 1; }
      98% { opacity: 0.94; }
    }

    /* ─── LOADING SCREEN ────────────────────────────────────── */
    #loader {
      position: fixed; inset: 0; z-index: 10000;
      background: var(--coal);
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      gap: 24px;
      transition: opacity 0.8s ease, visibility 0.8s;
    }
    #loader.hidden { opacity: 0; visibility: hidden; }

    .loader-gears {
      position: relative; width: 120px; height: 120px;
    }
    .loader-gear {
      position: absolute;
      border-radius: 50%;
      border: 4px solid var(--copper);
    }
    .loader-gear-big {
      width: 80px; height: 80px;
      top: 20px; left: 0;
      animation: spin 2s linear infinite;
      box-shadow: var(--glow-copper);
    }
    .loader-gear-small {
      width: 48px; height: 48px;
      top: 0; right: 0;
      animation: spin 1.2s linear infinite reverse;
      border-color: var(--brass);
      box-shadow: 0 0 15px rgba(200,149,10,0.5);
    }
    .loader-gear::before {
      content: '';
      position: absolute; inset: 8px;
      border-radius: 50%;
      background: var(--coal);
      border: 2px solid var(--copper-light);
    }

    .loader-text {
      font-family: var(--font-mono);
      color: var(--green-terminal);
      font-size: 13px;
      text-align: center;
      text-shadow: var(--glow-green);
      letter-spacing: 2px;
    }
    .loader-bar-wrap {
      width: 280px; height: 6px;
      background: var(--coal-light);
      border: 1px solid var(--copper);
      border-radius: 3px;
      overflow: hidden;
    }
    .loader-bar {
      height: 100%;
      background: linear-gradient(90deg, var(--copper), var(--brass-light));
      width: 0%;
      transition: width 0.3s ease;
      box-shadow: var(--glow-amber);
    }

    /* ─── CANVAS PARTICLES ──────────────────────────────────── */
    #particle-canvas {
      position: fixed; inset: 0;
      pointer-events: none; z-index: 0;
    }

    /* ─── MAIN LAYOUT ───────────────────────────────────────── */
    main { position: relative; z-index: 1; }

    section {
      position: relative;
      min-height: 100vh;
      padding: 80px 5vw;
    }

    /* ─── HERO SECTION ──────────────────────────────────────── */
    #hero {
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      overflow: hidden;
      background: radial-gradient(ellipse at 50% 60%, #2a1505 0%, var(--coal) 70%);
    }

    /* Background gear grid */
    .gear-bg {
      position: absolute; inset: 0;
      opacity: 0.06;
      background-image:
        url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='100' height='100' viewBox='0 0 100 100'%3E%3Ccircle cx='50' cy='50' r='40' fill='none' stroke='%23c8950a' stroke-width='2'/%3E%3Ccircle cx='50' cy='50' r='20' fill='none' stroke='%23c8950a' stroke-width='2'/%3E%3Cline x1='50' y1='0' x2='50' y2='100' stroke='%23c8950a' stroke-width='1'/%3E%3Cline x1='0' y1='50' x2='100' y2='50' stroke='%23c8950a' stroke-width='1'/%3E%3C/svg%3E");
      background-size: 100px 100px;
    }

    /* Decorative gears */
    .gear-deco {
      position: absolute;
      border-radius: 50%;
      border: 6px solid var(--copper);
      opacity: 0.18;
    }
    .gear-deco::before {
      content: '';
      position: absolute;
      inset: 12px;
      border-radius: 50%;
      border: 3px solid var(--copper-light);
    }
    .gear-deco::after {
      content: '';
      position: absolute;
      inset: 24px;
      border-radius: 50%;
      background: transparent;
      border: 2px dashed var(--brass);
    }
    .gear-deco.g1 {
      width: 300px; height: 300px;
      top: -80px; left: -80px;
      animation: spin 20s linear infinite;
      border-color: var(--brass);
    }
    .gear-deco.g2 {
      width: 180px; height: 180px;
      top: 10px; left: 160px;
      animation: spin 12s linear infinite reverse;
    }
    .gear-deco.g3 {
      width: 400px; height: 400px;
      bottom: -120px; right: -100px;
      animation: spin 28s linear infinite;
      border-color: var(--copper-light);
    }
    .gear-deco.g4 {
      width: 200px; height: 200px;
      bottom: 60px; right: 230px;
      animation: spin 16s linear infinite reverse;
      border-color: var(--brass);
    }

    @keyframes spin { to { transform: rotate(360deg); } }
    @keyframes spin-rev { to { transform: rotate(-360deg); } }

    /* Pipe lines */
    .pipe-h {
      position: absolute;
      height: 12px;
      background: linear-gradient(90deg, var(--coal), var(--brown), var(--copper), var(--brown), var(--coal));
      border-radius: 6px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.5);
    }
    .pipe-v {
      position: absolute;
      width: 12px;
      background: linear-gradient(180deg, var(--coal), var(--brown), var(--copper), var(--brown), var(--coal));
      border-radius: 6px;
      box-shadow: 2px 0 8px rgba(0,0,0,0.5);
    }
    .pipe-animated {
      animation: pipe-glow 3s ease-in-out infinite;
    }
    @keyframes pipe-glow {
      0%,100% { box-shadow: 0 0 4px rgba(181,82,10,0.2); }
      50% { box-shadow: 0 0 16px rgba(255,140,0,0.6), 0 0 30px rgba(181,82,10,0.3); }
    }

    /* Hero content */
    .hero-content {
      position: relative; z-index: 2;
      text-align: center;
      padding: 40px;
    }

    .hero-label {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--green-terminal);
      letter-spacing: 6px;
      text-transform: uppercase;
      text-shadow: var(--glow-green);
      margin-bottom: 16px;
      animation: blink-label 4s ease-in-out infinite;
    }
    @keyframes blink-label {
      0%,90%,100% { opacity: 1; }
      92% { opacity: 0.3; }
    }

    .hero-title {
      font-family: var(--font-display);
      font-size: clamp(28px, 5vw, 72px);
      font-weight: 900;
      color: var(--brass-light);
      text-shadow:
        0 0 20px rgba(200,149,10,0.8),
        0 0 60px rgba(181,82,10,0.4),
        3px 3px 0 var(--coal);
      line-height: 1.1;
      letter-spacing: 2px;
      margin-bottom: 8px;
    }

    .hero-surname {
      font-family: var(--font-display);
      font-size: clamp(20px, 3.5vw, 52px);
      font-weight: 400;
      color: var(--copper-light);
      letter-spacing: 8px;
      text-transform: uppercase;
      margin-bottom: 24px;
    }

    .hero-divider {
      width: 200px; height: 2px;
      background: linear-gradient(90deg, transparent, var(--copper), var(--brass), var(--copper), transparent);
      margin: 0 auto 24px;
      box-shadow: 0 0 10px rgba(181,82,10,0.5);
    }

    .hero-subtitle {
      font-family: var(--font-mono);
      font-size: clamp(12px, 1.8vw, 18px);
      color: var(--amber-pale);
      letter-spacing: 4px;
      min-height: 28px;
    }

    .cursor-blink {
      display: inline-block;
      width: 10px; height: 18px;
      background: var(--amber);
      margin-left: 2px;
      animation: cursor-blink 1s step-end infinite;
      vertical-align: middle;
    }
    @keyframes cursor-blink { 50% { opacity: 0; } }

    .hero-badges {
      display: flex; gap: 16px; flex-wrap: wrap;
      justify-content: center;
      margin-top: 32px;
    }

    .badge {
      padding: 6px 16px;
      border: 1px solid var(--copper);
      border-radius: 2px;
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--amber-pale);
      letter-spacing: 2px;
      background: rgba(181,82,10,0.08);
      position: relative;
      transition: all 0.3s;
    }
    .badge::before, .badge::after {
      content: '◆';
      position: absolute;
      top: 50%; transform: translateY(-50%);
      color: var(--copper);
      font-size: 8px;
    }
    .badge::before { left: -10px; }
    .badge::after { right: -10px; }
    .badge:hover {
      border-color: var(--brass);
      background: rgba(200,149,10,0.15);
      box-shadow: var(--glow-copper);
      color: var(--brass-light);
    }

    .hero-scroll-hint {
      position: absolute;
      bottom: 30px; left: 50%;
      transform: translateX(-50%);
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--copper);
      letter-spacing: 3px;
      animation: scroll-hint 2s ease-in-out infinite;
      text-align: center;
    }
    @keyframes scroll-hint {
      0%,100% { opacity: 0.4; transform: translateX(-50%) translateY(0); }
      50% { opacity: 1; transform: translateX(-50%) translateY(6px); }
    }

    /* ─── SECTION HEADERS ───────────────────────────────────── */
    .section-header {
      text-align: center;
      margin-bottom: 64px;
    }
    .section-num {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--green-terminal);
      letter-spacing: 6px;
      display: block;
      margin-bottom: 8px;
      text-shadow: var(--glow-green);
    }
    .section-title {
      font-family: var(--font-display);
      font-size: clamp(24px, 4vw, 48px);
      color: var(--brass-light);
      font-weight: 700;
      text-shadow: 0 0 30px rgba(200,149,10,0.5);
      letter-spacing: 4px;
    }
    .section-line {
      width: 120px; height: 2px;
      background: linear-gradient(90deg, transparent, var(--copper), transparent);
      margin: 16px auto 0;
    }

    /* ─── SECTION: ABOUT ────────────────────────────────────── */
    #about {
      background: var(--coal-mid);
    }

    .control-panel {
      max-width: 900px;
      margin: 0 auto;
      background: linear-gradient(145deg, var(--coal-light), var(--coal-mid));
      border: 2px solid var(--copper);
      border-radius: 4px;
      padding: 40px;
      position: relative;
      box-shadow:
        inset 0 0 40px rgba(0,0,0,0.5),
        0 0 40px rgba(181,82,10,0.15);
    }

    .panel-rivet {
      position: absolute;
      width: 16px; height: 16px;
      border-radius: 50%;
      background: radial-gradient(circle at 35% 35%, var(--steel), #222);
      border: 2px solid var(--brown);
      box-shadow: inset 0 1px 3px rgba(255,255,255,0.1);
    }
    .panel-rivet.tl { top: 10px; left: 10px; }
    .panel-rivet.tr { top: 10px; right: 10px; }
    .panel-rivet.bl { bottom: 10px; left: 10px; }
    .panel-rivet.br { bottom: 10px; right: 10px; }

    .panel-header {
      text-align: center;
      margin-bottom: 32px;
      padding-bottom: 16px;
      border-bottom: 1px solid var(--copper);
    }
    .panel-header-label {
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--copper);
      letter-spacing: 4px;
      text-transform: uppercase;
    }

    .panel-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }
    @media (max-width: 600px) { .panel-grid { grid-template-columns: 1fr; } }

    .panel-field {
      padding: 14px 16px;
      border: 1px solid var(--brown);
      border-radius: 2px;
      background: rgba(0,0,0,0.3);
      position: relative;
    }
    .panel-field-label {
      font-family: var(--font-mono);
      font-size: 9px;
      color: var(--copper);
      letter-spacing: 3px;
      margin-bottom: 4px;
      text-transform: uppercase;
    }
    .panel-field-value {
      font-family: var(--font-mono);
      font-size: 14px;
      color: var(--amber-pale);
    }
    .panel-field.full { grid-column: 1 / -1; }

    .status-indicator {
      display: flex; align-items: center; gap: 8px;
    }
    .status-dot {
      width: 10px; height: 10px;
      border-radius: 50%;
      background: var(--green-terminal);
      box-shadow: var(--glow-green);
      animation: pulse-dot 2s ease-in-out infinite;
    }
    @keyframes pulse-dot {
      0%,100% { transform: scale(1); opacity: 1; }
      50% { transform: scale(1.3); opacity: 0.7; }
    }

    .gauge-row {
      display: flex; gap: 20px;
      flex-wrap: wrap;
      margin-top: 24px;
      justify-content: center;
    }

    .gauge {
      display: flex; flex-direction: column;
      align-items: center; gap: 8px;
    }
    .gauge-circle {
      width: 70px; height: 70px;
      border-radius: 50%;
      border: 3px solid var(--copper);
      background: radial-gradient(circle, var(--coal-mid) 40%, var(--coal-light));
      position: relative;
      display: flex; align-items: center; justify-content: center;
      box-shadow: inset 0 0 10px rgba(0,0,0,0.5), 0 0 10px rgba(181,82,10,0.2);
    }
    .gauge-needle {
      position: absolute;
      width: 2px; height: 30px;
      background: var(--amber);
      bottom: 50%;
      left: 50%;
      transform-origin: bottom center;
      transform: translateX(-50%) rotate(var(--angle, -90deg));
      transition: transform 1.5s cubic-bezier(0.34, 1.56, 0.64, 1);
      border-radius: 1px;
      box-shadow: 0 0 4px var(--amber);
    }
    .gauge-center {
      width: 8px; height: 8px;
      border-radius: 50%;
      background: var(--copper);
      z-index: 1;
    }
    .gauge-label {
      font-family: var(--font-mono);
      font-size: 9px;
      color: var(--copper);
      letter-spacing: 2px;
      text-transform: uppercase;
      text-align: center;
    }
    .gauge-value {
      font-family: var(--font-tech);
      font-size: 11px;
      color: var(--amber);
      text-align: center;
    }

    /* ─── TECH ARSENAL ──────────────────────────────────────── */
    #skills {
      background: linear-gradient(180deg, var(--coal-mid), var(--coal));
    }

    .skills-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
      gap: 24px;
      max-width: 1100px;
      margin: 0 auto;
    }

    .skill-module {
      background: linear-gradient(145deg, var(--coal-light), var(--coal));
      border: 1px solid var(--brown);
      border-radius: 4px;
      padding: 24px 20px;
      text-align: center;
      position: relative;
      overflow: hidden;
      cursor: pointer;
      transition: all 0.4s ease;
      opacity: 0;
      transform: translateY(30px);
    }
    .skill-module.visible {
      opacity: 1;
      transform: translateY(0);
    }
    .skill-module:hover {
      border-color: var(--copper);
      transform: translateY(-4px);
      box-shadow: 0 8px 30px rgba(181,82,10,0.25);
    }
    .skill-module::before {
      content: '';
      position: absolute;
      top: 0; left: 0; right: 0;
      height: 2px;
      background: linear-gradient(90deg, transparent, var(--copper), transparent);
      opacity: 0;
      transition: opacity 0.3s;
    }
    .skill-module:hover::before { opacity: 1; }

    .skill-icon-wrap {
      width: 64px; height: 64px;
      margin: 0 auto 16px;
      position: relative;
    }

    .skill-ring {
      position: absolute; inset: 0;
      border-radius: 50%;
      border: 2px solid var(--copper);
      animation: spin 6s linear infinite;
    }
    .skill-ring-inner {
      position: absolute; inset: 8px;
      border-radius: 50%;
      border: 1px dashed var(--brass);
      animation: spin 4s linear infinite reverse;
    }

    .skill-letter {
      position: absolute; inset: 0;
      display: flex; align-items: center; justify-content: center;
      font-family: var(--font-tech);
      font-size: 18px;
      font-weight: 700;
      color: var(--amber);
      text-shadow: var(--glow-amber);
    }

    .skill-name {
      font-family: var(--font-tech);
      font-size: 13px;
      color: var(--brass-light);
      letter-spacing: 2px;
      margin-bottom: 8px;
    }

    .skill-desc {
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--copper);
      letter-spacing: 1px;
      line-height: 1.5;
    }

    .skill-bar-wrap {
      height: 4px;
      background: var(--coal-light);
      border-radius: 2px;
      margin-top: 12px;
      overflow: hidden;
    }
    .skill-bar {
      height: 100%;
      background: linear-gradient(90deg, var(--copper), var(--brass-light));
      width: 0%;
      border-radius: 2px;
      transition: width 1.5s cubic-bezier(0.4, 0, 0.2, 1);
      box-shadow: 0 0 6px var(--amber);
    }

    /* ─── PROJECT FORGE ─────────────────────────────────────── */
    #projects {
      background: var(--coal-mid);
    }

    .projects-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
      gap: 32px;
      max-width: 1200px;
      margin: 0 auto;
    }

    .project-machine {
      background: linear-gradient(145deg, var(--coal-light), var(--coal));
      border: 2px solid var(--brown);
      border-radius: 4px;
      overflow: hidden;
      position: relative;
      transition: all 0.4s ease;
      cursor: pointer;
      opacity: 0; transform: translateX(-20px);
    }
    .project-machine.visible { opacity: 1; transform: translateX(0); }
    .project-machine:hover { border-color: var(--copper); }
    .project-machine:hover .pm-header { background: linear-gradient(90deg, var(--rust), var(--coal-light)); }

    .pm-header {
      padding: 16px 20px;
      background: linear-gradient(90deg, var(--brown), var(--coal-light));
      display: flex; align-items: center; gap: 12px;
      transition: background 0.4s;
      border-bottom: 1px solid var(--brown);
    }
    .pm-icon {
      font-size: 24px;
      filter: drop-shadow(0 0 8px rgba(255,140,0,0.6));
    }
    .pm-title {
      font-family: var(--font-display);
      font-size: 15px;
      color: var(--brass-light);
      font-weight: 700;
      letter-spacing: 1px;
    }
    .pm-status {
      margin-left: auto;
      font-family: var(--font-mono);
      font-size: 9px;
      color: var(--green-terminal);
      text-shadow: var(--glow-green);
      letter-spacing: 2px;
    }

    .pm-body {
      padding: 20px;
    }
    .pm-desc {
      font-family: var(--font-mono);
      font-size: 12px;
      color: var(--amber-pale);
      line-height: 1.7;
      margin-bottom: 16px;
    }

    .pm-features {
      list-style: none;
      margin-bottom: 16px;
    }
    .pm-features li {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--copper-light);
      padding: 3px 0;
      padding-left: 16px;
      position: relative;
    }
    .pm-features li::before {
      content: '▸';
      position: absolute;
      left: 0;
      color: var(--copper);
    }

    .pm-tech-tags {
      display: flex; flex-wrap: wrap; gap: 8px;
    }
    .pm-tag {
      padding: 3px 10px;
      border: 1px solid var(--copper);
      border-radius: 2px;
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--amber-pale);
      background: rgba(181,82,10,0.1);
      letter-spacing: 1px;
    }

    .pm-animation {
      height: 60px;
      background: var(--coal);
      border-top: 1px solid var(--brown);
      display: flex; align-items: center;
      padding: 0 20px;
      gap: 12px;
      overflow: hidden;
    }

    .conveyor {
      flex: 1; height: 8px;
      background: repeating-linear-gradient(
        -45deg,
        var(--brown) 0px,
        var(--brown) 4px,
        var(--coal-light) 4px,
        var(--coal-light) 8px
      );
      border-radius: 4px;
      background-size: 200% 100%;
      animation: conveyor-move 2s linear infinite;
    }
    @keyframes conveyor-move { to { background-position: -100% 0; } }

    .conveyor-box {
      width: 20px; height: 20px;
      background: var(--copper);
      border-radius: 2px;
      animation: box-move 3s ease-in-out infinite;
    }
    @keyframes box-move {
      0%,100% { transform: translateX(0) rotate(0deg); }
      50% { transform: translateX(10px) rotate(5deg); }
    }

    /* ─── GITHUB ENGINE ─────────────────────────────────────── */
    #github {
      background: linear-gradient(180deg, var(--coal), var(--coal-mid));
    }

    .reactor-wrap {
      max-width: 800px;
      margin: 0 auto;
      text-align: center;
    }

    .reactor-core {
      width: 200px; height: 200px;
      margin: 0 auto 40px;
      position: relative;
    }
    .reactor-ring {
      position: absolute; border-radius: 50%;
      border-style: solid;
    }
    .reactor-ring-1 {
      inset: 0;
      border-width: 4px;
      border-color: var(--copper);
      animation: spin 8s linear infinite;
      box-shadow: 0 0 20px rgba(181,82,10,0.3);
    }
    .reactor-ring-2 {
      inset: 16px;
      border-width: 3px;
      border-color: var(--brass);
      border-style: dashed;
      animation: spin 5s linear infinite reverse;
    }
    .reactor-ring-3 {
      inset: 32px;
      border-width: 2px;
      border-color: var(--copper-light);
      animation: spin 12s linear infinite;
    }
    .reactor-center {
      position: absolute;
      inset: 48px;
      border-radius: 50%;
      background: radial-gradient(circle, var(--amber) 0%, var(--copper) 40%, var(--coal) 70%);
      animation: reactor-pulse 2s ease-in-out infinite;
      display: flex; align-items: center; justify-content: center;
    }
    @keyframes reactor-pulse {
      0%,100% { box-shadow: 0 0 20px rgba(255,140,0,0.5), 0 0 60px rgba(181,82,10,0.3); }
      50% { box-shadow: 0 0 40px rgba(255,140,0,0.8), 0 0 100px rgba(181,82,10,0.5); }
    }
    .reactor-icon {
      font-size: 28px;
      filter: drop-shadow(0 0 10px rgba(255,140,0,0.8));
    }

    .github-stats-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
      gap: 20px;
      margin-bottom: 32px;
    }

    .stat-box {
      background: var(--coal-light);
      border: 1px solid var(--brown);
      border-radius: 4px;
      padding: 20px 16px;
      text-align: center;
      transition: all 0.3s;
    }
    .stat-box:hover {
      border-color: var(--copper);
      box-shadow: 0 0 20px rgba(181,82,10,0.2);
    }
    .stat-num {
      font-family: var(--font-tech);
      font-size: 28px;
      color: var(--amber);
      text-shadow: var(--glow-amber);
      display: block;
    }
    .stat-label {
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--copper);
      letter-spacing: 2px;
      text-transform: uppercase;
      margin-top: 4px;
      display: block;
    }

    .github-link-btn {
      display: inline-flex; align-items: center; gap: 10px;
      padding: 12px 32px;
      border: 2px solid var(--copper);
      border-radius: 2px;
      font-family: var(--font-mono);
      font-size: 13px;
      color: var(--amber-pale);
      letter-spacing: 3px;
      text-decoration: none;
      background: rgba(181,82,10,0.1);
      transition: all 0.3s;
    }
    .github-link-btn:hover {
      background: rgba(181,82,10,0.25);
      box-shadow: var(--glow-copper);
      transform: translateY(-2px);
    }

    /* ─── NETWORK TERMINAL ──────────────────────────────────── */
    #contact {
      background: var(--coal);
    }

    .network-devices {
      display: flex;
      flex-wrap: wrap;
      gap: 32px;
      justify-content: center;
      max-width: 900px;
      margin: 0 auto;
    }

    .net-device {
      width: 240px;
      background: linear-gradient(145deg, var(--coal-light), var(--coal));
      border: 2px solid var(--brown);
      border-radius: 4px;
      padding: 28px 24px;
      text-align: center;
      position: relative;
      transition: all 0.3s;
      cursor: pointer;
      text-decoration: none;
      display: block;
    }
    .net-device::before {
      content: '';
      position: absolute;
      top: -1px; left: 20%; right: 20%;
      height: 3px;
      background: var(--copper);
      border-radius: 0 0 2px 2px;
      transition: left 0.3s, right 0.3s;
    }
    .net-device:hover::before { left: 10%; right: 10%; background: var(--brass-light); }
    .net-device:hover {
      border-color: var(--copper);
      transform: translateY(-4px);
      box-shadow: 0 10px 30px rgba(181,82,10,0.2);
    }

    .nd-icon {
      font-size: 36px;
      margin-bottom: 12px;
      display: block;
      filter: drop-shadow(0 0 8px rgba(255,140,0,0.5));
    }
    .nd-type {
      font-family: var(--font-mono);
      font-size: 9px;
      color: var(--copper);
      letter-spacing: 4px;
      text-transform: uppercase;
      margin-bottom: 8px;
    }
    .nd-name {
      font-family: var(--font-display);
      font-size: 15px;
      color: var(--brass-light);
      font-weight: 700;
      margin-bottom: 8px;
    }
    .nd-value {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--amber-pale);
      letter-spacing: 1px;
      word-break: break-all;
    }

    .nd-light {
      position: absolute;
      top: 12px; right: 12px;
      width: 8px; height: 8px;
      border-radius: 50%;
      background: var(--green-terminal);
      box-shadow: var(--glow-green);
      animation: pulse-dot 2.5s ease-in-out infinite;
    }

    /* ─── FOOTER ────────────────────────────────────────────── */
    footer {
      text-align: center;
      padding: 40px 20px;
      border-top: 1px solid var(--brown);
      background: var(--coal);
    }
    footer p {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--copper);
      letter-spacing: 2px;
    }
    footer .footer-ascii {
      font-family: var(--font-mono);
      font-size: 9px;
      color: var(--brown);
      margin-bottom: 16px;
      line-height: 1.3;
    }

    /* ─── FLOATING NAV ──────────────────────────────────────── */
    .float-nav {
      position: fixed;
      right: 20px;
      top: 50%;
      transform: translateY(-50%);
      z-index: 100;
      display: flex; flex-direction: column; gap: 12px;
    }
    .fnav-dot {
      width: 10px; height: 10px;
      border-radius: 50%;
      background: var(--brown);
      border: 1px solid var(--copper);
      cursor: pointer;
      transition: all 0.3s;
      position: relative;
    }
    .fnav-dot:hover, .fnav-dot.active {
      background: var(--amber);
      box-shadow: var(--glow-amber);
      transform: scale(1.4);
    }
    .fnav-dot::after {
      content: attr(data-label);
      position: absolute;
      right: 18px; top: 50%;
      transform: translateY(-50%);
      font-family: var(--font-mono);
      font-size: 10px;
      color: var(--copper);
      letter-spacing: 2px;
      white-space: nowrap;
      opacity: 0;
      transition: opacity 0.2s;
      pointer-events: none;
      background: var(--coal);
      padding: 2px 8px;
      border: 1px solid var(--brown);
    }
    .fnav-dot:hover::after { opacity: 1; }

    /* ─── HIDDEN TERMINAL ───────────────────────────────────── */
    #terminal-overlay {
      position: fixed; inset: 0;
      z-index: 9998;
      background: rgba(0,0,0,0.92);
      display: flex; align-items: center; justify-content: center;
      opacity: 0; visibility: hidden;
      transition: opacity 0.3s, visibility 0.3s;
    }
    #terminal-overlay.open { opacity: 1; visibility: visible; }

    .terminal-box {
      width: min(700px, 95vw);
      background: var(--coal);
      border: 2px solid var(--green-terminal);
      border-radius: 4px;
      overflow: hidden;
      box-shadow: 0 0 60px rgba(0,255,65,0.3);
    }
    .term-header {
      padding: 8px 16px;
      background: var(--coal-light);
      border-bottom: 1px solid var(--green-term-dim);
      display: flex; align-items: center; gap: 10px;
    }
    .term-dot {
      width: 10px; height: 10px;
      border-radius: 50%;
    }
    .term-dot.red { background: #ff5f57; }
    .term-dot.yellow { background: #febc2e; }
    .term-dot.green-d { background: #2bc840; }
    .term-title {
      font-family: var(--font-mono);
      font-size: 11px;
      color: var(--green-term-dim);
      letter-spacing: 3px;
      margin-left: auto;
    }
    .term-body {
      padding: 20px;
      height: 380px;
      overflow-y: auto;
      font-family: var(--font-mono);
      font-size: 13px;
      line-height: 1.8;
    }
    .term-line { color: var(--green-terminal); }
    .term-line.dim { color: var(--green-term-dim); }
    .term-line.white { color: var(--amber-pale); }
    .term-line.yellow { color: var(--brass-light); }
    .term-line.error { color: #ff6060; }

    .term-input-row {
      display: flex; align-items: center; gap: 8px;
      padding: 12px 20px;
      border-top: 1px solid var(--green-term-dim);
    }
    .term-prompt { color: var(--green-terminal); font-family: var(--font-mono); font-size: 13px; }
    .term-input {
      flex: 1;
      background: transparent;
      border: none;
      outline: none;
      font-family: var(--font-mono);
      font-size: 13px;
      color: var(--green-terminal);
      caret-color: var(--green-terminal);
    }

    /* ─── OVERDRIVE MODE ────────────────────────────────────── */
    body.overdrive {
      animation: overdrive-shake 0.1s ease-in-out infinite;
    }
    body.overdrive .gear-deco { animation-duration: 1s !important; }
    body.overdrive .reactor-ring-1 { animation-duration: 1s !important; }
    body.overdrive .reactor-ring-2 { animation-duration: 0.6s !important; }
    body.overdrive .reactor-core { animation-duration: 0.3s !important; }
    @keyframes overdrive-shake {
      0%,100% { filter: hue-rotate(0deg) brightness(1); }
      25% { filter: hue-rotate(5deg) brightness(1.1); }
      75% { filter: hue-rotate(-5deg) brightness(0.95); }
    }

    .overdrive-banner {
      position: fixed;
      top: 20px; left: 50%;
      transform: translateX(-50%);
      z-index: 9997;
      padding: 10px 30px;
      background: var(--coal);
      border: 2px solid var(--amber);
      font-family: var(--font-tech);
      font-size: 14px;
      color: var(--amber);
      letter-spacing: 6px;
      text-shadow: var(--glow-amber);
      box-shadow: var(--glow-amber);
      display: none;
      animation: overdrive-pulse 0.5s ease-in-out infinite;
    }
    .overdrive-banner.show { display: block; }
    @keyframes overdrive-pulse {
      0%,100% { opacity: 1; }
      50% { opacity: 0.6; }
    }

    /* ─── PARALLAX MOUSE ────────────────────────────────────── */
    .parallax-slow { transition: transform 0.3s ease-out; }
    .parallax-fast { transition: transform 0.15s ease-out; }

    /* ─── OBSERVER ANIMATIONS ───────────────────────────────── */
    .fade-up {
      opacity: 0; transform: translateY(30px);
      transition: opacity 0.6s ease, transform 0.6s ease;
    }
    .fade-up.visible { opacity: 1; transform: translateY(0); }

    /* ─── RESPONSIVE ────────────────────────────────────────── */
    @media (max-width: 768px) {
      section { padding: 60px 5vw; }
      .float-nav { display: none; }
      .network-devices { gap: 16px; }
      .net-device { width: 100%; }
    }
  </style>
</head>
<body>

<!-- CRT overlay -->
<div class="crt-overlay"></div>

<!-- Loading Screen -->
<div id="loader">
  <div class="loader-gears">
    <div class="loader-gear loader-gear-big"></div>
    <div class="loader-gear loader-gear-small"></div>
  </div>
  <div class="loader-text" id="loader-text">INITIALIZING BRASS WORKSHOP...</div>
  <div class="loader-bar-wrap">
    <div class="loader-bar" id="loader-bar"></div>
  </div>
</div>

<!-- Particle canvas -->
<canvas id="particle-canvas"></canvas>

<!-- Overdrive banner -->
<div class="overdrive-banner" id="overdrive-banner">⚙ OVERDRIVE MODE ACTIVATED ⚙</div>

<!-- Floating nav -->
<nav class="float-nav" id="float-nav">
  <div class="fnav-dot active" data-target="hero" data-label="ENGINE ROOM"></div>
  <div class="fnav-dot" data-target="about" data-label="CONTROL PANEL"></div>
  <div class="fnav-dot" data-target="skills" data-label="TECH ARSENAL"></div>
  <div class="fnav-dot" data-target="projects" data-label="PROJECT FORGE"></div>
  <div class="fnav-dot" data-target="github" data-label="REACTOR CORE"></div>
  <div class="fnav-dot" data-target="contact" data-label="NETWORK"></div>
</nav>

<!-- Hidden terminal overlay -->
<div id="terminal-overlay">
  <div class="terminal-box">
    <div class="term-header">
      <div class="term-dot red"></div>
      <div class="term-dot yellow"></div>
      <div class="term-dot green-d"></div>
      <div class="term-title">BRASS WORKSHOP TERMINAL v1.0</div>
    </div>
    <div class="term-body" id="term-body">
      <div class="term-line">THE BRASS WORKSHOP TERMINAL</div>
      <div class="term-line dim">Type 'help' for available commands.</div>
      <div class="term-line dim">Press [~] or [ESC] to close.</div>
      <div class="term-line"> </div>
    </div>
    <div class="term-input-row">
      <span class="term-prompt">workshop@reactor:~$</span>
      <input class="term-input" id="term-input" type="text" autocomplete="off" spellcheck="false" placeholder="" />
    </div>
  </div>
</div>

<main>
  <!-- ══════════════════════════════════════════════════════════
       SECTION 1 — HERO / ENGINE ROOM
  ══════════════════════════════════════════════════════════ -->
  <section id="hero">
    <div class="gear-bg"></div>

    <!-- Decorative gears -->
    <div class="gear-deco g1 parallax-slow"></div>
    <div class="gear-deco g2 parallax-fast"></div>
    <div class="gear-deco g3 parallax-slow"></div>
    <div class="gear-deco g4 parallax-fast"></div>

    <!-- Pipes -->
    <div class="pipe-h pipe-animated" style="left:0;right:0;top:30%"></div>
    <div class="pipe-h pipe-animated" style="left:0;right:0;bottom:25%"></div>
    <div class="pipe-v pipe-animated" style="top:0;bottom:0;left:8%"></div>
    <div class="pipe-v pipe-animated" style="top:0;bottom:0;right:8%"></div>

    <div class="hero-content">
      <div class="hero-label" id="hero-init-label">◈ ENGINE INITIALIZED ◈</div>

      <h1 class="hero-title" id="hero-name">RONALDO</h1>
      <div class="hero-surname">DA SILVA MENDONÇA JÚNIOR</div>

      <div class="hero-divider"></div>

      <div class="hero-subtitle">
        <span id="hero-typewriter"></span><span class="cursor-blink"></span>
      </div>

      <div class="hero-badges">
        <span class="badge">SYSTEMS DEVELOPER</span>
        <span class="badge">IF — INSTITUTO FEDERAL</span>
        <span class="badge">CAMPOS DO JORDÃO · SP</span>
        <span class="badge">ADS STUDENT</span>
      </div>
    </div>

    <div class="hero-scroll-hint">▾ SCROLL TO EXPLORE ▾</div>
  </section>

  <!-- ══════════════════════════════════════════════════════════
       SECTION 2 — IDENTIFICATION / ABOUT
  ══════════════════════════════════════════════════════════ -->
  <section id="about">
    <div class="section-header fade-up">
      <span class="section-num">// SECTION 02</span>
      <h2 class="section-title">CONTROL PANEL</h2>
      <div class="section-line"></div>
    </div>

    <div class="control-panel fade-up">
      <div class="panel-rivet tl"></div>
      <div class="panel-rivet tr"></div>
      <div class="panel-rivet bl"></div>
      <div class="panel-rivet br"></div>

      <div class="panel-header">
        <div class="panel-header-label">◈ OPERATOR IDENTIFICATION — UNIT #001 ◈</div>
      </div>

      <div class="panel-grid">
        <div class="panel-field">
          <div class="panel-field-label">▸ DESIGNATION</div>
          <div class="panel-field-value">Ronaldo da Silva Mendonça Jr.</div>
        </div>
        <div class="panel-field">
          <div class="panel-field-label">▸ STATION</div>
          <div class="panel-field-value">Campos do Jordão — SP, Brazil</div>
        </div>
        <div class="panel-field">
          <div class="panel-field-label">▸ CLEARANCE LEVEL</div>
          <div class="panel-field-value">
            <div class="status-indicator">
              <div class="status-dot"></div>
              ADS STUDENT — ACTIVE OPERATOR
            </div>
          </div>
        </div>
        <div class="panel-field">
          <div class="panel-field-label">▸ INSTITUTE</div>
          <div class="panel-field-value">Instituto Federal de São Paulo</div>
        </div>
        <div class="panel-field full">
          <div class="panel-field-label">▸ PRIMARY DIRECTIVES</div>
          <div class="panel-field-value" style="line-height:1.8">
            Systems development · Immersive interfaces · Interactive experiences<br/>
            Automation · Visual creativity · Experimental web design
          </div>
        </div>
        <div class="panel-field full">
          <div class="panel-field-label">▸ OPERATOR LOG</div>
          <div class="panel-field-value" style="line-height:1.8; font-size:13px; color: var(--amber-pale)">
            Recently graduated IT Technician — now engineering the future through code, systems architecture and
            immersive digital experiences. Every project is a machine. Every machine runs on passion.
          </div>
        </div>
      </div>

      <div class="gauge-row">
        <div class="gauge">
          <div class="gauge-circle">
            <div class="gauge-needle" style="--angle: 60deg"></div>
            <div class="gauge-center"></div>
          </div>
          <div class="gauge-label">DEDICATION</div>
          <div class="gauge-value">95%</div>
        </div>
        <div class="gauge">
          <div class="gauge-circle">
            <div class="gauge-needle" style="--angle: 40deg"></div>
            <div class="gauge-center"></div>
          </div>
          <div class="gauge-label">CREATIVITY</div>
          <div class="gauge-value">90%</div>
        </div>
        <div class="gauge">
          <div class="gauge-circle">
            <div class="gauge-needle" style="--angle: 20deg"></div>
            <div class="gauge-center"></div>
          </div>
          <div class="gauge-label">LEARNING</div>
          <div class="gauge-value">MAX</div>
        </div>
        <div class="gauge">
          <div class="gauge-circle">
            <div class="gauge-needle" style="--angle: 50deg"></div>
            <div class="gauge-center"></div>
          </div>
          <div class="gauge-label">CURIOSITY</div>
          <div class="gauge-value">∞</div>
        </div>
      </div>
    </div>
  </section>

  <!-- ══════════════════════════════════════════════════════════
       SECTION 3 — TECH ARSENAL
  ══════════════════════════════════════════════════════════ -->
  <section id="skills">
    <div class="section-header fade-up">
      <span class="section-num">// SECTION 03</span>
      <h2 class="section-title">TECH ARSENAL</h2>
      <div class="section-line"></div>
    </div>

    <div class="skills-grid">
      <div class="skill-module" data-level="85">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">C#</div>
        </div>
        <div class="skill-name">C SHARP</div>
        <div class="skill-desc">Mechanical Reactor Core<br/>⚡ Energy rings · Electrical arcs</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
      <div class="skill-module" data-level="80">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">JS</div>
        </div>
        <div class="skill-name">JAVASCRIPT</div>
        <div class="skill-desc">Tesla Coil Generator<br/>⚡ Electric sparks · Energy pulses</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
      <div class="skill-module" data-level="75">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">⬡</div>
        </div>
        <div class="skill-name">NODE.JS</div>
        <div class="skill-desc">Green Pipe Systems<br/>◈ Fluid simulation · Data flow</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
      <div class="skill-module" data-level="82">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">SQL</div>
        </div>
        <div class="skill-name">SQL / DATABASES</div>
        <div class="skill-desc">Industrial Data Tanks<br/>◈ PostgreSQL · SQL Server · MySQL</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
      <div class="skill-module" data-level="90">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">&lt;/&gt;</div>
        </div>
        <div class="skill-name">HTML & CSS</div>
        <div class="skill-desc">Engineering Blueprints<br/>◈ Wireframes · Holographic drafts</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
      <div class="skill-module" data-level="72">
        <div class="skill-icon-wrap">
          <div class="skill-ring"></div>
          <div class="skill-ring-inner"></div>
          <div class="skill-letter">⚙</div>
        </div>
        <div class="skill-name">SYSTEMS DESIGN</div>
        <div class="skill-desc">Master Gear Assembly<br/>◈ Architecture · Automation</div>
        <div class="skill-bar-wrap"><div class="skill-bar"></div></div>
      </div>
    </div>
  </section>

  <!-- ══════════════════════════════════════════════════════════
       SECTION 4 — PROJECT FORGE
  ══════════════════════════════════════════════════════════ -->
  <section id="projects">
    <div class="section-header fade-up">
      <span class="section-num">// SECTION 04</span>
      <h2 class="section-title">PROJECT FORGE</h2>
      <div class="section-line"></div>
    </div>

    <div class="projects-grid">
      <div class="project-machine">
        <div class="pm-header">
          <span class="pm-icon">🍽</span>
          <div class="pm-title">RESTAURANT ENGINE</div>
          <div class="pm-status">◈ ONLINE</div>
        </div>
        <div class="pm-body">
          <p class="pm-desc">A mechanical restaurant operation engine — full management system for orders, tables, inventory and billing.</p>
          <ul class="pm-features">
            <li>Animated receipt printer simulation</li>
            <li>Rotating order management system</li>
            <li>Industrial cash register interface</li>
            <li>Real-time table status tracking</li>
          </ul>
          <div class="pm-tech-tags">
            <span class="pm-tag">C#</span>
            <span class="pm-tag">SQL SERVER</span>
            <span class="pm-tag">.NET</span>
          </div>
        </div>
        <div class="pm-animation">
          <div class="conveyor-box"></div>
          <div class="conveyor"></div>
          <div class="conveyor-box"></div>
        </div>
      </div>

      <div class="project-machine">
        <div class="pm-header">
          <span class="pm-icon">🛍</span>
          <div class="pm-title">CLOTHING STORE SYSTEM</div>
          <div class="pm-status">◈ ONLINE</div>
        </div>
        <div class="pm-body">
          <p class="pm-desc">An automated industrial warehouse — complete clothing store management with inventory, POS and logistics.</p>
          <ul class="pm-features">
            <li>Robotic inventory shelf management</li>
            <li>POS system with fiscal operations</li>
            <li>Freight calculation engine</li>
            <li>Order tracking and shipping control</li>
          </ul>
          <div class="pm-tech-tags">
            <span class="pm-tag">C#</span>
            <span class="pm-tag">SQL SERVER</span>
            <span class="pm-tag">WinForms</span>
          </div>
        </div>
        <div class="pm-animation">
          <div class="conveyor"></div>
          <div class="conveyor-box"></div>
          <div class="conveyor"></div>
        </div>
      </div>

      <div class="project-machine">
        <div class="pm-header">
          <span class="pm-icon">🧾</span>
          <div class="pm-title">POS TERMINAL</div>
          <div class="pm-status">◈ ONLINE</div>
        </div>
        <div class="pm-body">
          <p class="pm-desc">A retro-futuristic Victorian terminal — complete point-of-sale system with fiscal printing simulation.</p>
          <ul class="pm-features">
            <li>CRT-style interface design</li>
            <li>Physical animated button system</li>
            <li>Fiscal receipt printer simulation</li>
            <li>Transaction history and reports</li>
          </ul>
          <div class="pm-tech-tags">
            <span class="pm-tag">C#</span>
            <span class="pm-tag">SQL SERVER</span>
          </div>
        </div>
        <div class="pm-animation">
          <div class="conveyor-box"></div>
          <div class="conveyor"></div>
          <div class="conveyor-box"></div>
        </div>
      </div>

      <div class="project-machine">
        <div class="pm-header">
          <span class="pm-icon">🌐</span>
          <div class="pm-title">E-COMMERCE PLATFORM</div>
          <div class="pm-status">◈ ONLINE</div>
        </div>
        <div class="pm-body">
          <p class="pm-desc">Three independent store systems — full-featured e-commerce with inventory, logistics and admin dashboard.</p>
          <ul class="pm-features">
            <li>Inventory management system</li>
            <li>Freight calculation & tracking</li>
            <li>Admin dashboard interface</li>
            <li>Responsive design across devices</li>
          </ul>
          <div class="pm-tech-tags">
            <span class="pm-tag">JavaScript</span>
            <span class="pm-tag">Node.js</span>
            <span class="pm-tag">PostgreSQL</span>
            <span class="pm-tag">HTML/CSS</span>
          </div>
        </div>
        <div class="pm-animation">
          <div class="conveyor"></div>
          <div class="conveyor-box"></div>
          <div class="conveyor"></div>
        </div>
      </div>
    </div>
  </section>

  <!-- ══════════════════════════════════════════════════════════
       SECTION 5 — GITHUB ENGINE CORE
  ══════════════════════════════════════════════════════════ -->
  <section id="github">
    <div class="section-header fade-up">
      <span class="section-num">// SECTION 05</span>
      <h2 class="section-title">REACTOR CORE</h2>
      <div class="section-line"></div>
    </div>

    <div class="reactor-wrap">
      <div class="reactor-core fade-up">
        <div class="reactor-ring reactor-ring-1"></div>
        <div class="reactor-ring reactor-ring-2"></div>
        <div class="reactor-ring reactor-ring-3"></div>
        <div class="reactor-center">
          <span class="reactor-icon">⚙</span>
        </div>
      </div>

      <div class="github-stats-grid fade-up">
        <div class="stat-box">
          <span class="stat-num" id="stat-repos">—</span>
          <span class="stat-label">Repositories</span>
        </div>
        <div class="stat-box">
          <span class="stat-num" id="stat-followers">—</span>
          <span class="stat-label">Followers</span>
        </div>
        <div class="stat-box">
          <span class="stat-num" id="stat-following">—</span>
          <span class="stat-label">Following</span>
        </div>
        <div class="stat-box">
          <span class="stat-num">∞</span>
          <span class="stat-label">Commits/Passion</span>
        </div>
      </div>

      <a href="https://github.com/ronaldoribeirosm" target="_blank" rel="noopener" class="github-link-btn fade-up">
        ⚙ ENTER THE ENGINE ROOM
      </a>
    </div>
  </section>

  <!-- ══════════════════════════════════════════════════════════
       SECTION 6 — NETWORK TERMINAL / CONTACT
  ══════════════════════════════════════════════════════════ -->
  <section id="contact">
    <div class="section-header fade-up">
      <span class="section-num">// SECTION 06</span>
      <h2 class="section-title">NETWORK TERMINAL</h2>
      <div class="section-line"></div>
    </div>

    <div class="network-devices">
      <a href="https://github.com/ronaldoribeirosm" target="_blank" rel="noopener" class="net-device fade-up">
        <div class="nd-light"></div>
        <span class="nd-icon">⚙</span>
        <div class="nd-type">◈ INDUSTRIAL TERMINAL</div>
        <div class="nd-name">GitHub</div>
        <div class="nd-value">@ronaldoribeirosm</div>
      </a>

      <a href="https://www.linkedin.com/in/ronaldo-ribeiro-2256a43a8/" target="_blank" rel="noopener" class="net-device fade-up">
        <div class="nd-light" style="animation-delay:0.5s"></div>
        <span class="nd-icon">📡</span>
        <div class="nd-type">◈ RADIO COMMUNICATION</div>
        <div class="nd-name">LinkedIn</div>
        <div class="nd-value">ronaldo-ribeiro-2256a43a8</div>
      </a>

      <a href="mailto:ronaldoribeirosm@gmail.com" class="net-device fade-up">
        <div class="nd-light" style="animation-delay:1s"></div>
        <span class="nd-icon">📮</span>
        <div class="nd-type">◈ PNEUMATIC TUBE</div>
        <div class="nd-name">Email</div>
        <div class="nd-value">ronaldoribeirosm@gmail.com</div>
      </a>
    </div>
  </section>
</main>

<!-- ─── FOOTER ───────────────────────────────────────────── -->
<footer>
  <div class="footer-ascii">
    ╔══════════════════════════════════════════════════════╗<br/>
    ║     THE BRASS WORKSHOP  ◈  ALL SYSTEMS OPERATIONAL   ║<br/>
    ╚══════════════════════════════════════════════════════╝
  </div>
  <p>© 2025 RONALDO DA SILVA MENDONÇA JÚNIOR ◈ CAMPOS DO JORDÃO · SP · BRAZIL</p>
  <p style="margin-top:8px; font-size:10px; color: var(--brown)">
    Press [~] to open terminal · ↑↑↓↓←→←→BA for OVERDRIVE MODE
  </p>
</footer>

<!-- ═══════════════════════════════════════════════════════════
     JAVASCRIPT ENGINE
════════════════════════════════════════════════════════════ -->
<script>
/* ── LOADER ─────────────────────────────────────────────── */
(function () {
  const bar = document.getElementById('loader-bar');
  const text = document.getElementById('loader-text');
  const loader = document.getElementById('loader');

  const steps = [
    [10, 'IGNITING BOILER SYSTEMS...'],
    [25, 'PRESSURIZING STEAM LINES...'],
    [40, 'ENGAGING PRIMARY GEARS...'],
    [55, 'CALIBRATING GAUGES...'],
    [70, 'LOADING MECHANICAL MODULES...'],
    [85, 'ACTIVATING AMBER LIGHTING...'],
    [95, 'SYNCHRONIZING ENGINE CORE...'],
    [100, 'BRASS WORKSHOP INITIALIZED.'],
  ];

  let i = 0;
  function next() {
    if (i >= steps.length) {
      setTimeout(() => loader.classList.add('hidden'), 600);
      return;
    }
    const [pct, msg] = steps[i++];
    bar.style.width = pct + '%';
    text.textContent = msg;
    setTimeout(next, 320 + Math.random() * 180);
  }
  next();
})();

/* ── PARTICLE SYSTEM (steam + sparks) ───────────────────── */
(function () {
  const canvas = document.getElementById('particle-canvas');
  const ctx = canvas.getContext('2d');
  let W, H, particles = [];

  function resize() {
    W = canvas.width = window.innerWidth;
    H = canvas.height = window.innerHeight;
  }
  window.addEventListener('resize', resize);
  resize();

  class Particle {
    constructor(type) {
      this.reset(type);
    }
    reset(type) {
      this.type = type || (Math.random() < 0.85 ? 'steam' : 'spark');
      this.x = Math.random() * W;
      this.y = H + 10;
      if (this.type === 'steam') {
        this.vx = (Math.random() - 0.5) * 0.5;
        this.vy = -(0.4 + Math.random() * 0.8);
        this.size = 4 + Math.random() * 12;
        this.alpha = 0.05 + Math.random() * 0.12;
        this.life = 1;
        this.decay = 0.003 + Math.random() * 0.004;
        this.hue = 30 + Math.random() * 20;
      } else {
        this.x = Math.random() * W;
        this.y = Math.random() * H * 0.6;
        this.vx = (Math.random() - 0.5) * 3;
        this.vy = (Math.random() - 0.5) * 3;
        this.size = 1 + Math.random() * 2;
        this.alpha = 0.8 + Math.random() * 0.2;
        this.life = 1;
        this.decay = 0.04 + Math.random() * 0.06;
      }
    }
    update() {
      this.x += this.vx;
      this.y += this.vy;
      this.life -= this.decay;
      if (this.type === 'steam') {
        this.size += 0.1;
        this.vx += (Math.random() - 0.5) * 0.05;
      }
    }
    draw() {
      ctx.save();
      ctx.globalAlpha = this.alpha * this.life;
      if (this.type === 'steam') {
        const grad = ctx.createRadialGradient(this.x, this.y, 0, this.x, this.y, this.size);
        grad.addColorStop(0, `hsla(${this.hue}, 40%, 70%, 0.3)`);
        grad.addColorStop(1, 'transparent');
        ctx.fillStyle = grad;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.fill();
      } else {
        ctx.fillStyle = `rgba(255, 180, 50, ${this.alpha * this.life})`;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.fill();
        ctx.fillStyle = 'rgba(255,255,200,0.9)';
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size * 0.3, 0, Math.PI * 2);
        ctx.fill();
      }
      ctx.restore();
    }
  }

  for (let i = 0; i < 60; i++) {
    const p = new Particle('steam');
    p.y = Math.random() * H;
    p.life = Math.random();
    particles.push(p);
  }
  for (let i = 0; i < 10; i++) {
    particles.push(new Particle('spark'));
  }

  let mouseX = W / 2, mouseY = H / 2;
  document.addEventListener('mousemove', e => { mouseX = e.clientX; mouseY = e.clientY; });

  function loop() {
    ctx.clearRect(0, 0, W, H);
    particles.forEach(p => {
      p.update();
      p.draw();
      if (p.life <= 0) p.reset();
    });

    // occasional spark burst near mouse
    if (Math.random() < 0.03) {
      const sp = new Particle('spark');
      sp.x = mouseX + (Math.random() - 0.5) * 60;
      sp.y = mouseY + (Math.random() - 0.5) * 60;
      particles.push(sp);
      if (particles.length > 120) particles.splice(0, 1);
    }

    requestAnimationFrame(loop);
  }
  loop();
})();

/* ── TYPEWRITER ─────────────────────────────────────────── */
(function () {
  const el = document.getElementById('hero-typewriter');
  const phrases = [
    'Student · Developer · Systems Builder',
    'Engineering Immersive Experiences',
    'Brass, Code & Creativity',
    'The Machine Lives.',
    'From Campos do Jordão to the World',
  ];
  let pi = 0, ci = 0, deleting = false, wait = 0;

  function tick() {
    const phrase = phrases[pi];
    if (wait > 0) { wait--; setTimeout(tick, 80); return; }
    if (!deleting) {
      el.textContent = phrase.slice(0, ++ci);
      if (ci === phrase.length) { deleting = true; wait = 28; }
    } else {
      el.textContent = phrase.slice(0, --ci);
      if (ci === 0) { deleting = false; pi = (pi + 1) % phrases.length; wait = 8; }
    }
    setTimeout(tick, deleting ? 40 : 90 + Math.random() * 40);
  }
  tick();
})();

/* ── PARALLAX MOUSE ─────────────────────────────────────── */
(function () {
  const slow = document.querySelectorAll('.parallax-slow');
  const fast = document.querySelectorAll('.parallax-fast');
  document.addEventListener('mousemove', e => {
    const cx = window.innerWidth / 2, cy = window.innerHeight / 2;
    const dx = (e.clientX - cx) / cx, dy = (e.clientY - cy) / cy;
    slow.forEach(el => { el.style.transform = `translate(${dx*12}px,${dy*12}px)`; });
    fast.forEach(el => { el.style.transform = `translate(${dx*24}px,${dy*24}px)`; });
  });
})();

/* ── INTERSECTION OBSERVER ──────────────────────────────── */
(function () {
  const opts = { threshold: 0.15 };
  const obs = new IntersectionObserver((entries) => {
    entries.forEach(e => {
      if (e.isIntersecting) {
        e.target.classList.add('visible');
        if (e.target.classList.contains('skill-module')) {
          const bar = e.target.querySelector('.skill-bar');
          const level = e.target.dataset.level || '70';
          setTimeout(() => bar.style.width = level + '%', 200);
        }
      }
    });
  }, opts);

  document.querySelectorAll('.fade-up, .skill-module, .project-machine').forEach(el => obs.observe(el));
})();

/* ── FLOATING NAV ───────────────────────────────────────── */
(function () {
  const dots = document.querySelectorAll('.fnav-dot');
  const sections = ['hero','about','skills','projects','github','contact'];

  dots.forEach(dot => {
    dot.addEventListener('click', () => {
      const id = dot.dataset.target;
      document.getElementById(id)?.scrollIntoView({ behavior: 'smooth' });
    });
  });

  const obs = new IntersectionObserver((entries) => {
    entries.forEach(e => {
      if (e.isIntersecting) {
        const idx = sections.indexOf(e.target.id);
        dots.forEach((d,i) => d.classList.toggle('active', i === idx));
      }
    });
  }, { threshold: 0.4 });

  sections.forEach(id => {
    const el = document.getElementById(id);
    if (el) obs.observe(el);
  });
})();

/* ── GAUGE ANIMATION on scroll ──────────────────────────── */
(function () {
  const obs = new IntersectionObserver(entries => {
    entries.forEach(e => {
      if (e.isIntersecting) {
        e.target.querySelectorAll('.gauge-needle').forEach(n => {
          const current = parseFloat(getComputedStyle(n).getPropertyValue('--angle'));
          n.style.transform = `translateX(-50%) rotate(${n.style.getPropertyValue('--angle') || '-90deg'})`;
        });
      }
    });
  }, { threshold: 0.4 });
  const aboutSec = document.getElementById('about');
  if (aboutSec) obs.observe(aboutSec);
})();

/* ── GITHUB STATS ───────────────────────────────────────── */
(function () {
  fetch('https://api.github.com/users/ronaldoribeirosm')
    .then(r => r.json())
    .then(data => {
      document.getElementById('stat-repos').textContent = data.public_repos ?? '—';
      document.getElementById('stat-followers').textContent = data.followers ?? '—';
      document.getElementById('stat-following').textContent = data.following ?? '—';
    })
    .catch(() => {});
})();

/* ── HIDDEN TERMINAL ────────────────────────────────────── */
(function () {
  const overlay = document.getElementById('terminal-overlay');
  const body = document.getElementById('term-body');
  const input = document.getElementById('term-input');
  let open = false;

  function openTerm() { open = true; overlay.classList.add('open'); setTimeout(() => input.focus(), 100); }
  function closeTerm() { open = false; overlay.classList.remove('open'); }

  document.addEventListener('keydown', e => {
    if (e.key === '`' || e.key === '~') { e.preventDefault(); open ? closeTerm() : openTerm(); }
    if (e.key === 'Escape' && open) closeTerm();
  });
  overlay.addEventListener('click', e => { if (e.target === overlay) closeTerm(); });

  function addLine(text, cls = '') {
    const div = document.createElement('div');
    div.className = 'term-line' + (cls ? ' ' + cls : '');
    div.textContent = text;
    body.appendChild(div);
    body.scrollTop = body.scrollHeight;
  }

  const commands = {
    help: () => {
      addLine('');
      addLine('AVAILABLE COMMANDS:', 'yellow');
      addLine('  about          — Operator identification');
      addLine('  projects       — List forge projects');
      addLine('  skills         — Tech arsenal manifest');
      addLine('  contact        — Network terminal links');
      addLine('  system-status  — Workshop diagnostics');
      addLine('  clear          — Clear terminal');
      addLine('  overdrive      — ⚠ ENGAGE OVERDRIVE MODE');
      addLine('');
    },
    about: () => {
      addLine('');
      addLine('◈ OPERATOR: Ronaldo da Silva Mendonça Jr.', 'yellow');
      addLine('  STATION  : Campos do Jordão · SP · Brazil');
      addLine('  STATUS   : ADS Student — Active Operator');
      addLine('  INSTITUTE: Instituto Federal de São Paulo');
      addLine('  PASSIONS : Systems · Interfaces · Automation');
      addLine('');
    },
    projects: () => {
      addLine('');
      addLine('◈ PROJECT FORGE MANIFEST:', 'yellow');
      addLine('  [1] Restaurant Management System   — C# / SQL Server');
      addLine('  [2] Clothing Store System          — C# / SQL Server');
      addLine('  [3] POS Terminal                   — C# / SQL Server');
      addLine('  [4] E-Commerce Platform (x3)       — JS / Node / PostgreSQL');
      addLine('');
    },
    skills: () => {
      addLine('');
      addLine('◈ TECH ARSENAL:', 'yellow');
      addLine('  C#           ██████████████████░░  85%');
      addLine('  JavaScript   ████████████████░░░░  80%');
      addLine('  Node.js      ███████████████░░░░░  75%');
      addLine('  SQL/Database ████████████████░░░░  82%');
      addLine('  HTML & CSS   ██████████████████░░  90%');
      addLine('');
    },
    contact: () => {
      addLine('');
      addLine('◈ NETWORK TERMINAL LINKS:', 'yellow');
      addLine('  GitHub   : https://github.com/ronaldoribeirosm');
      addLine('  LinkedIn : linkedin.com/in/ronaldo-ribeiro-2256a43a8');
      addLine('  Email    : ronaldoribeirosm@gmail.com');
      addLine('');
    },
    'system-status': () => {
      addLine('');
      addLine('◈ BRASS WORKSHOP DIAGNOSTICS:', 'yellow');
      addLine('  Boiler pressure   : NOMINAL  ██████████ 100%');
      addLine('  Gear systems      : RUNNING  ████████░░  82%');
      addLine('  Steam pressure    : OPTIMAL  █████████░  91%');
      addLine('  Amber lighting    : ACTIVE   ██████████ 100%');
      addLine('  Particle engine   : ONLINE   ██████████ 100%');
      addLine('  Creativity index  : OVERFLOW ██████████ MAX');
      addLine('');
      addLine('  ALL SYSTEMS OPERATIONAL.', 'dim');
      addLine('');
    },
    overdrive: () => {
      addLine('');
      addLine('⚠ ENGAGING OVERDRIVE MODE...', 'yellow');
      addLine('⚠ WARNING: EXTREME POWER OUTPUT', 'error');
      setTimeout(() => {
        document.body.classList.add('overdrive');
        document.getElementById('overdrive-banner').classList.add('show');
        addLine('✓ OVERDRIVE MODE ACTIVE — ALL SYSTEMS MAXED', 'yellow');
        addLine('');
        setTimeout(() => {
          document.body.classList.remove('overdrive');
          document.getElementById('overdrive-banner').classList.remove('show');
        }, 10000);
      }, 1000);
    },
    clear: () => {
      body.innerHTML = '';
    },
  };

  input.addEventListener('keydown', e => {
    if (e.key !== 'Enter') return;
    const cmd = input.value.trim().toLowerCase();
    input.value = '';
    addLine(`workshop@reactor:~$ ${cmd}`, 'dim');
    if (cmd === '') return;
    if (commands[cmd]) {
      commands[cmd]();
    } else {
      addLine(`command not found: ${cmd}`, 'error');
      addLine("Type 'help' for available commands.", 'dim');
    }
  });
})();

/* ── KONAMI CODE → OVERDRIVE ────────────────────────────── */
(function () {
  const seq = ['ArrowUp','ArrowUp','ArrowDown','ArrowDown','ArrowLeft','ArrowRight','ArrowLeft','ArrowRight','b','a'];
  let idx = 0;
  document.addEventListener('keydown', e => {
    if (e.key === seq[idx]) {
      idx++;
      if (idx === seq.length) {
        idx = 0;
        document.body.classList.add('overdrive');
        document.getElementById('overdrive-banner').classList.add('show');
        setTimeout(() => {
          document.body.classList.remove('overdrive');
          document.getElementById('overdrive-banner').classList.remove('show');
        }, 12000);
      }
    } else {
      idx = (e.key === seq[0]) ? 1 : 0;
    }
  });
})();

/* ── SMOOTH REVEAL ANIMATION on load ────────────────────── */
(function () {
  const hero = document.getElementById('hero');
  setTimeout(() => {
    hero.querySelectorAll('.gear-deco, .pipe-h, .pipe-v').forEach((el,i) => {
      el.style.opacity = '0';
      setTimeout(() => { el.style.transition = 'opacity 1.5s ease'; el.style.opacity = ''; }, i * 100 + 800);
    });
  }, 100);
})();
</script>
</body>
</html>
