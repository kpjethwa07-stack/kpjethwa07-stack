<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Alex Rivera — Interactive Digital Universe</title>
<style>
  :root{
    --paper:#f2ece0;
    --paper-2:#eae2d2;
    --ink:#2c261e;
    --ink-soft:#514a3e;
    --moss:#5c6b45;
    --clay:#b8674a;
    --line:#cfc4ac;
    --mono: 'JetBrains Mono', monospace;
  }
  @import url('https://fonts.googleapis.com/css2?family=Fraunces:ital,wght@0,300;0,500;0,700;1,500;1,600&family=Work+Sans:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap');
  *{margin:0;padding:0;box-sizing:border-box;}
  html{scroll-behavior:smooth;}
  body{
    background:var(--paper);
    transition:background-color .9s ease, color .9s ease;
    color:var(--ink);
    font-family:'Work Sans',sans-serif;
    overflow-x:hidden;
    cursor:none;
    position:relative;
  }
  /* paper grain overlay */
  body::before{
    content:'';position:fixed;inset:0;z-index:1;pointer-events:none;
    opacity:.5;mix-blend-mode:multiply;
    background-image:url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='180' height='180'><filter id='n'><feTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='2' stitchTiles='stitch'/><feColorMatrix type='saturate' values='0'/></filter><rect width='100%25' height='100%25' filter='url(%23n)' opacity='0.35'/></svg>");
  }
  @media (max-width:820px){ body{cursor:auto;} }
  ::selection{background:var(--clay);color:var(--paper);}

  /* ---------- preloader ---------- */
  #preloader{
    position:fixed;inset:0;z-index:999;background:var(--paper);
    display:flex;flex-direction:column;align-items:center;justify-content:center;
    transition:opacity .8s ease, visibility .8s ease;
  }
  #preloader.hide{opacity:0;visibility:hidden;pointer-events:none;}
  .pre-label{font-family:var(--mono);font-size:.7rem;letter-spacing:.3em;color:#8a7f6c;margin-bottom:1.2rem;}
  .pre-bar{width:200px;height:1px;background:var(--line);position:relative;overflow:hidden;}
  .pre-fill{position:absolute;left:0;top:0;height:100%;width:0%;background:var(--clay);}
  .pre-pct{font-family:var(--mono);font-size:.7rem;color:var(--clay);margin-top:1rem;}

  /* ---------- custom cursor ---------- */
  #cursor-dot,#cursor-ring{position:fixed;top:0;left:0;pointer-events:none;z-index:998;border-radius:50%;transform:translate(-50%,-50%);}
  #cursor-dot{width:6px;height:6px;background:var(--clay);}
  #cursor-ring{width:34px;height:34px;border:1px solid rgba(44,38,30,0.35);transition:width .25s,height .25s,border-color .25s,background .25s;}
  #cursor-ring.hover{width:64px;height:64px;background:rgba(184,103,74,0.18);border-color:var(--clay);}
  #cursor-ring span{position:absolute;inset:0;display:none;align-items:center;justify-content:center;font-family:var(--mono);font-size:.6rem;letter-spacing:.1em;color:var(--ink);}
  #cursor-ring.hover span{display:flex;}
  @media (max-width:820px){ #cursor-dot,#cursor-ring{display:none;} }

  /* ---------- nav ---------- */
  nav{
    position:fixed;top:0;left:0;right:0;z-index:100;
    display:flex;justify-content:space-between;align-items:center;
    padding:1.4rem 2.4rem;font-family:var(--mono);font-size:.72rem;letter-spacing:.15em;
    background:color-mix(in srgb, var(--paper) 72%, transparent);
    transition:background-color .9s ease, border-color .9s ease;
    backdrop-filter:blur(10px);
    border-bottom:1px solid var(--line);
  }
  nav .links{display:flex;gap:2rem;list-style:none;}
  nav .links a{color:var(--ink);text-decoration:none;opacity:.65;transition:opacity .3s;}
  nav .links a:hover{opacity:1;}
  nav .logo{cursor:pointer;font-weight:500;font-family:'Fraunces',serif;font-style:italic;letter-spacing:.02em;}

  section{position:relative;min-height:100vh;padding:0 2.4rem;}

  /* ---------- hero ---------- */
  #hero{display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;}
  #hero-canvas{position:absolute;inset:0;z-index:0;}
  .hero-content{position:relative;z-index:2;}
  .eyebrow{font-family:var(--mono);font-size:.75rem;letter-spacing:.35em;color:var(--clay);margin-bottom:1.4rem;transition:color .9s ease;}
  h1.hero-name{
    font-family:'Fraunces',serif;font-style:italic;font-size:clamp(3rem,8.5vw,7rem);font-weight:600;line-height:.98;letter-spacing:-.01em;
  }
  .hero-roles{margin-top:1.2rem;font-family:var(--mono);font-size:.85rem;letter-spacing:.15em;color:#8a7f6c;}
  .scroll-cue{position:absolute;bottom:2.5rem;left:50%;transform:translateX(-50%);font-family:var(--mono);font-size:.65rem;letter-spacing:.25em;color:#8a7f6c;z-index:2;}
  .scroll-cue .line{width:1px;height:34px;background:#8a7f6c;margin:.6rem auto 0;animation:scrollpulse 2s ease-in-out infinite;}
  @keyframes scrollpulse{0%,100%{opacity:.2;transform:scaleY(.4);}50%{opacity:1;transform:scaleY(1);}}

  /* ---------- intro ---------- */
  #intro{display:flex;align-items:center;justify-content:center;text-align:center;}
  .intro-line{font-family:'Fraunces',serif;font-size:clamp(1.8rem,5vw,3.4rem);font-weight:500;line-height:1.25;opacity:.15;transition:opacity .6s;}
  .intro-line.on{opacity:1;}
  .intro-line .accent{color:var(--clay);transition:color .9s ease;}

  /* ---------- project ---------- */
  #work{padding-top:6rem;}
  .project{
    min-height:100vh;display:flex;flex-direction:column;justify-content:center;
    border-top:1px solid var(--line);padding:4rem 0;position:relative;
  }
  .proj-num{font-family:var(--mono);font-size:.75rem;color:var(--clay);letter-spacing:.2em;transition:color .9s ease;}
  .proj-title{font-family:'Fraunces',serif;font-style:italic;font-size:clamp(2.4rem,6.5vw,5rem);font-weight:600;margin:.6rem 0 1rem;letter-spacing:-.005em;}
  .proj-meta{display:flex;gap:2rem;flex-wrap:wrap;font-family:var(--mono);font-size:.7rem;letter-spacing:.1em;color:#8a7f6c;margin-bottom:1.6rem;}
  .proj-desc{max-width:520px;color:var(--ink-soft);font-weight:300;line-height:1.6;margin-bottom:2rem;}
  .proj-visual{
    width:100%;height:46vh;border-radius:2px;position:relative;overflow:hidden;
    background:linear-gradient(135deg,#e9e1d0,#f0e9db);
    border:1px solid var(--line);
  }
  #proj-canvas{width:100%;height:100%;display:block;}
  .proj-link{font-family:var(--mono);font-size:.7rem;letter-spacing:.12em;color:var(--ink);text-decoration:none;border-bottom:1px solid var(--clay);padding-bottom:2px;}

  /* ---------- about / timeline ---------- */
  #about{padding-top:6rem;border-top:1px solid var(--line);}
  .about-title{font-family:'Fraunces',serif;font-size:clamp(2.2rem,5.5vw,3.8rem);font-weight:500;margin-bottom:3rem;position:relative;display:inline-block;}
  .heading-underline{position:absolute;left:-4%;bottom:-.35em;width:108%;height:14px;overflow:visible;pointer-events:none;}
  .heading-underline path{stroke:var(--clay);stroke-width:3;fill:none;stroke-linecap:round;stroke-dasharray:260;stroke-dashoffset:260;transition:stroke-dashoffset 1s cubic-bezier(.65,0,.35,1);}
  .heading-underline.on path{stroke-dashoffset:0;}

  /* word-by-word wobble reveal */
  .wobble-word{display:inline-block;opacity:0;transform:translateY(.5em) rotate(-2deg);transition:opacity .6s cubic-bezier(.16,1,.3,1), transform .6s cubic-bezier(.16,1,.3,1);}
  .wobble-word.on{opacity:1;transform:translateY(0) rotate(0deg);}
  .timeline{max-width:640px;}
  .tl-row{display:flex;gap:2rem;padding:1.4rem 0;border-top:1px solid var(--line);opacity:.3;transform:translateX(-14px);transition:opacity .6s, transform .6s;}
  .tl-row.on{opacity:1;transform:translateX(0);}
  .tl-year{font-family:var(--mono);color:var(--clay);width:70px;flex-shrink:0;transition:color .9s ease;}
  .tl-text{color:var(--ink-soft);font-weight:300;}

  /* ---------- skills constellation ---------- */
  #skills{padding-top:6rem;border-top:1px solid var(--line);}
  #skills-canvas{width:100%;height:60vh;display:block;}
  .skills-title{font-family:'Fraunces',serif;font-size:clamp(2.2rem,5.5vw,3.8rem);font-weight:500;margin-bottom:1rem;position:relative;display:inline-block;}
  .skills-hint{font-family:var(--mono);font-size:.7rem;color:#8a7f6c;letter-spacing:.1em;margin-bottom:1.5rem;}

  /* ---------- contact ---------- */
  #contact{
    border-top:1px solid var(--line);display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;
  }
  .contact-statement{font-family:'Fraunces',serif;font-style:italic;font-size:clamp(2.2rem,6.5vw,4.6rem);font-weight:500;max-width:900px;line-height:1.15;margin-bottom:2.5rem;}
  .contact-links{display:flex;gap:2.4rem;flex-wrap:wrap;justify-content:center;font-family:var(--mono);font-size:.85rem;letter-spacing:.1em;}
  .contact-links a{color:var(--ink);text-decoration:none;opacity:.7;transition:opacity .3s;}
  .contact-links a:hover{opacity:1;color:var(--clay);}
  footer{padding:2.4rem;text-align:center;font-family:var(--mono);font-size:.65rem;color:#a89a80;letter-spacing:.1em;}

  a,button{outline-offset:4px;}
  a:focus-visible,button:focus-visible,.logo:focus-visible{outline:2px solid var(--clay);}

  /* ---------- glass panels ---------- */
  .glass{
    background:rgba(255,255,255,0.35);
    backdrop-filter:blur(14px) saturate(120%);
    -webkit-backdrop-filter:blur(14px) saturate(120%);
    border:1px solid rgba(44,38,30,0.09);
    border-radius:18px;
    position:relative;
    overflow:hidden;
    transition:transform .5s cubic-bezier(.16,1,.3,1), border-color .4s, box-shadow .4s;
  }
  .glass::before{
    content:'';position:absolute;inset:0;
    background:linear-gradient(120deg, transparent 30%, rgba(184,103,74,0.16) 48%, transparent 66%);
    transform:translateX(-120%);
    transition:transform .9s cubic-bezier(.16,1,.3,1);
    pointer-events:none;
  }
  .glass:hover{
    border-color:rgba(184,103,74,0.5);
    box-shadow:0 20px 50px -22px rgba(92,107,69,0.35);
    transform:translateY(-4px) rotate(-.2deg);
  }
  .glass:hover::before{ transform:translateX(120%); }

  .proj-visual.glass{background:rgba(255,255,255,0.3);}
  .timeline .tl-row{border-radius:14px;}
  .tl-row.glass{padding:1.4rem 1.6rem;border-top:none;margin-bottom:1rem;}

  /* ---------- background SVG vectors (scroll reveal) ---------- */
  .bg-svg{
    position:absolute;pointer-events:none;z-index:0;opacity:0;
    transform:scale(.35) translateZ(0);
    filter:blur(14px);
    transition:opacity 1.1s cubic-bezier(.16,1,.3,1), transform 1.1s cubic-bezier(.16,1,.3,1), filter 1.1s cubic-bezier(.16,1,.3,1);
  }
  .bg-svg.on{ opacity:.55; transform:scale(1) translateZ(0); filter:blur(0px); }
  .bg-svg.dim.on{ opacity:.22; }
  section{overflow:visible;}


  @media (prefers-reduced-motion: reduce){
    *{animation-duration:.001ms !important;animation-iteration-count:1 !important;transition-duration:.001ms !important;scroll-behavior:auto !important;}
  }
</style>
</head>
<body>

<div id="preloader">
  <div class="pre-label">INITIALIZING EXPERIENCE</div>
  <div class="pre-bar"><div class="pre-fill" id="preFill"></div></div>
  <div class="pre-pct" id="prePct">00%</div>
</div>

<div id="cursor-dot"></div>
<div id="cursor-ring"><span id="cursorText"></span></div>

<nav>
  <div class="logo" id="logoBtn" tabindex="0" role="button" aria-label="Trigger signature interaction">A. RIVERA</div>
  <ul class="links">
    <li><a href="#hero">INDEX</a></li>
    <li><a href="#work">WORK</a></li>
    <li><a href="#about">ABOUT</a></li>
    <li><a href="#skills">SKILLS</a></li>
    <li><a href="#contact">CONTACT</a></li>
  </ul>
</nav>

<section id="hero">
  <canvas id="hero-canvas"></canvas>
  <div class="hero-content">
    <div class="eyebrow">AN INTERACTIVE DIGITAL UNIVERSE</div>
    <h1 class="hero-name" id="heroName">ALEX RIVERA</h1>
    <div class="hero-roles">CREATIVE DEVELOPER · 3D EXPERIMENTER · INTERACTION DESIGNER</div>
  </div>
  <div class="scroll-cue">SCROLL TO ENTER<div class="line"></div></div>
</section>

<section id="intro">
  <svg class="bg-svg" data-reveal style="top:8%;left:4%;width:260px;height:260px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M100,14 C150,10 188,54 186,102 C184,152 148,190 98,188 C50,186 12,148 14,98 C16,50 54,18 100,14 Z" stroke="var(--clay)" stroke-width="1.4" stroke-linecap="round"/>
    <path d="M100,52 C124,50 148,74 146,100 C144,126 122,148 98,146 C74,144 54,122 56,98" stroke="var(--ink-soft)" stroke-opacity=".4" stroke-width="1"/>
  </svg>
  <svg class="bg-svg dim" data-reveal style="bottom:6%;right:6%;width:220px;height:220px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M100,16 L184,148 Q186,154 180,153 L22,150 Q14,149 18,142 Z" stroke="var(--ink-soft)" stroke-width="1.2" stroke-linejoin="round"/>
  </svg>
  <div>
    <div class="intro-line" data-line data-wobble>I DON'T JUST BUILD WEBSITES.</div>
    <div class="intro-line" data-line data-wobble>I BUILD <span class="accent">EXPERIENCES.</span></div>
  </div>
</section>

<section id="work">
  <svg class="bg-svg dim" data-reveal style="top:2%;right:2%;width:300px;height:300px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M18,102 C22,42 176,158 182,98" stroke="var(--clay)" stroke-width="1.4" stroke-linecap="round"/>
    <path d="M18,58 C58,18 142,182 182,138" stroke="var(--ink-soft)" stroke-opacity=".3" stroke-width="1"/>
  </svg>
  <div class="project">
    <svg class="bg-svg" data-reveal style="bottom:4%;left:0%;width:180px;height:180px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
      <path d="M34,42 C90,20 168,36 166,96 C164,160 96,182 38,164 C10,154 8,66 34,42 Z" stroke="var(--clay)" stroke-width="1.3" stroke-linecap="round"/>
    </svg>
    <span class="proj-num">PROJECT 01</span>
    <h2 class="proj-title">NEBULA</h2>
    <div class="proj-meta"><span>2026</span><span>ROLE — LEAD DEV / 3D</span><span>THREE.JS / REACT / WEBGL</span></div>
    <p class="proj-desc">A generative visual instrument that turns live audio input into a field of drifting particle nebulae. Built for a music label's launch site.</p>
    <div class="proj-visual glass"><canvas id="proj-canvas"></canvas></div>
    <div style="margin-top:1.4rem;"><a class="proj-link" href="#" onclick="return false;">VIEW PROJECT →</a></div>
  </div>
</section>

<section id="about">
  <svg class="bg-svg dim" data-reveal style="top:4%;left:2%;width:240px;height:240px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
    <circle cx="60" cy="60" r="4" fill="#7c3cff"/><circle cx="140" cy="90" r="4" fill="#7c3cff"/>
    <circle cx="90" cy="150" r="4" fill="var(--ink-soft)"/>
    <path d="M60,60 C90,68 118,80 140,90" stroke="var(--clay)" stroke-opacity=".45" stroke-width="1"/>
    <path d="M140,90 C122,112 106,132 90,150" stroke="var(--clay)" stroke-opacity=".45" stroke-width="1"/>
  </svg>
  <h2 class="about-title" data-underline>ABOUT
    <svg class="heading-underline" viewBox="0 0 300 14"><path d="M4,8 C60,2 120,12 160,6 C210,-1 260,10 296,5"/></svg>
  </h2>
  <div class="timeline">
    <div class="tl-row glass" data-tl><span class="tl-year">2023</span><span class="tl-text">Started building for the web, obsessively.</span></div>
    <div class="tl-row glass" data-tl><span class="tl-year">2024</span><span class="tl-text">Moved from static sites into motion and creative development.</span></div>
    <div class="tl-row glass" data-tl><span class="tl-year">2025</span><span class="tl-text">Entered 3D and WebGL — first shader, first shipped R3F scene.</span></div>
    <div class="tl-row glass" data-tl><span class="tl-year">2026</span><span class="tl-text">Building immersive digital experiences full-time.</span></div>
  </div>
</section>

<section id="skills">
  <h2 class="skills-title" data-underline>SKILLS
    <svg class="heading-underline" viewBox="0 0 300 14"><path d="M4,8 C60,2 120,12 160,6 C210,-1 260,10 296,5"/></svg>
  </h2>
  <div class="skills-hint">HOVER A NODE TO HIGHLIGHT RELATED TECHNOLOGIES</div>
  <div class="glass" style="padding:1rem;">
    <canvas id="skills-canvas"></canvas>
  </div>
</section>

<section id="contact">
  <svg class="bg-svg" data-reveal style="top:6%;left:50%;transform-origin:center;margin-left:-140px;width:280px;height:280px;" viewBox="0 0 200 200" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path d="M100,8 C154,6 194,50 192,102 C190,156 148,196 96,192 C46,188 6,146 10,94 C14,44 52,10 100,8 Z" stroke="var(--clay)" stroke-width="1.4" stroke-linecap="round"/>
    <path d="M100,42 C130,40 158,64 156,96 C154,130 128,154 96,152" stroke="var(--ink-soft)" stroke-opacity=".35" stroke-width="1"/>
  </svg>
  <div class="contact-statement" data-wobble>LET'S BUILD SOMETHING IMPOSSIBLE.</div>
  <div class="contact-links">
    <a href="mailto:hello@alexrivera.dev">EMAIL</a>
    <a href="#" onclick="return false;">GITHUB</a>
    <a href="#" onclick="return false;">LINKEDIN</a>
    <a href="#" onclick="return false;">INSTAGRAM</a>
  </div>
</section>

<footer>© 2026 ALEX RIVERA — PLACEHOLDER TEST BUILD</footer>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script>
/* ---------------- preloader ---------------- */
(function(){
  const fill=document.getElementById('preFill'), pct=document.getElementById('prePct'), pre=document.getElementById('preloader');
  let p=0;
  const t=setInterval(()=>{
    p+=Math.random()*18;
    if(p>=100){p=100;clearInterval(t);setTimeout(()=>pre.classList.add('hide'),300);}
    fill.style.width=p+'%';
    pct.textContent=String(Math.floor(p)).padStart(2,'0')+'%';
  },140);
})();

/* ---------------- custom cursor ---------------- */
(function(){
  const dot=document.getElementById('cursor-dot'), ring=document.getElementById('cursor-ring'), text=document.getElementById('cursorText');
  let rx=0,ry=0,mx=0,my=0;
  window.addEventListener('mousemove',e=>{
    dot.style.left=e.clientX+'px'; dot.style.top=e.clientY+'px';
    mx=e.clientX; my=e.clientY;
  });
  function loop(){ rx+=(mx-rx)*.18; ry+=(my-ry)*.18; ring.style.left=rx+'px'; ring.style.top=ry+'px'; requestAnimationFrame(loop);}
  loop();
  document.querySelectorAll('a,button,.logo').forEach(el=>{
    el.addEventListener('mouseenter',()=>{ring.classList.add('hover'); text.textContent = el.classList.contains('logo')?'PLAY':(el.classList.contains('proj-link')?'VIEW':'');});
    el.addEventListener('mouseleave',()=>{ring.classList.remove('hover'); text.textContent='';});
  });
})();

/* ---------------- hero 3D scene ---------------- */
(function(){
  const canvas=document.getElementById('hero-canvas');
  const reduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  const renderer=new THREE.WebGLRenderer({canvas, antialias:true, alpha:true});
  renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  renderer.setSize(window.innerWidth, window.innerHeight);
  const scene=new THREE.Scene();
  const camera=new THREE.PerspectiveCamera(45, window.innerWidth/window.innerHeight, .1, 100);
  camera.position.set(0,0,7);

  const geo=new THREE.IcosahedronGeometry(1.7,1);
  const mat=new THREE.MeshStandardMaterial({color:0xc98a6b, metalness:.15, roughness:.55, emissive:0xb8674a, emissiveIntensity:.08});
  const obj=new THREE.Mesh(geo,mat);
  scene.add(obj);

  const key=new THREE.PointLight(0xe3b48a, 44, 20); key.position.set(4,3,4); scene.add(key);
  const fill=new THREE.PointLight(0xffffff, 8, 20); fill.position.set(-4,-2,3); scene.add(fill);
  scene.add(new THREE.AmbientLight(0xe9dcc4,1.1));

  // particles
  const pCount = window.innerWidth<820?250:700;
  const pGeo=new THREE.BufferGeometry();
  const positions=new Float32Array(pCount*3);
  for(let i=0;i<pCount;i++){
    positions[i*3]=(Math.random()-.5)*16;
    positions[i*3+1]=(Math.random()-.5)*10;
    positions[i*3+2]=(Math.random()-.5)*10;
  }
  pGeo.setAttribute('position', new THREE.BufferAttribute(positions,3));
  const pMat=new THREE.PointsMaterial({color:0xb8674a, size:.02, transparent:true, opacity:.4});
  const points=new THREE.Points(pGeo,pMat);
  scene.add(points);

  let mx=0,my=0, tx=0,ty=0;
  window.addEventListener('mousemove',e=>{
    mx=(e.clientX/window.innerWidth-.5)*2;
    my=(e.clientY/window.innerHeight-.5)*2;
  });
  window.addEventListener('resize',()=>{
    renderer.setSize(window.innerWidth, window.innerHeight);
    camera.aspect=window.innerWidth/window.innerHeight;
    camera.updateProjectionMatrix();
  });

  let scrollY=0;
  window.addEventListener('scroll',()=>{ scrollY=window.scrollY; });

  function animate(){
    requestAnimationFrame(animate);
    tx += (mx-tx)*.04; ty += (my-ty)*.04;
    if(!reduced){
      obj.rotation.y += 0.0022 + tx*0.001;
      obj.rotation.x += 0.001 + ty*0.0006;
      points.rotation.y += 0.0006;
      camera.position.x = tx*.6;
      camera.position.y = -ty*.4 - scrollY*0.0012;
      camera.lookAt(0,0,0);
    }
    renderer.render(scene,camera);
  }
  animate();
})();

/* ---------------- project mini 3D ---------------- */
(function(){
  const canvas=document.getElementById('proj-canvas');
  const renderer=new THREE.WebGLRenderer({canvas, antialias:true, alpha:true});
  const parent = canvas.parentElement;
  function size(){ renderer.setSize(parent.clientWidth, parent.clientHeight); }
  size();
  const scene=new THREE.Scene();
  const camera=new THREE.PerspectiveCamera(45, parent.clientWidth/parent.clientHeight, .1, 100);
  camera.position.set(0,0,5);
  const geo=new THREE.TorusKnotGeometry(1,.32,140,16);
  const mat=new THREE.MeshStandardMaterial({color:0xc98a6b, metalness:.1, roughness:.5, emissive:0xb8674a, emissiveIntensity:.12});
  const knot=new THREE.Mesh(geo,mat);
  scene.add(knot);
  scene.add(new THREE.PointLight(0xe3b48a,34,20).translateX(3).translateY(2).translateZ(3));
  scene.add(new THREE.AmbientLight(0xe9dcc4,1));
  let hovered=false;
  canvas.addEventListener('pointerenter',()=>hovered=true);
  canvas.addEventListener('pointerleave',()=>hovered=false);
  window.addEventListener('resize',()=>{
    size();
    camera.aspect=parent.clientWidth/parent.clientHeight;
    camera.updateProjectionMatrix();
  });
  function animate(){
    requestAnimationFrame(animate);
    knot.rotation.y += hovered?0.012:0.004;
    knot.rotation.x += 0.002;
    renderer.render(scene,camera);
  }
  animate();
})();

/* ---------------- intro reveal on scroll ---------------- */
(function(){
  const lines=document.querySelectorAll('[data-line]');
  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{ en.target.classList.toggle('on', en.isIntersecting); });
  },{threshold:.6});
  lines.forEach(l=>io.observe(l));
})();

/* ---------------- timeline reveal ---------------- */
(function(){
  const rows=document.querySelectorAll('[data-tl]');
  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{ en.target.classList.toggle('on', en.isIntersecting); });
  },{threshold:.3});
  rows.forEach(r=>io.observe(r));
})();

/* ---------------- background SVG scroll reveal + parallax ---------------- */
(function(){
  const svgs=document.querySelectorAll('[data-reveal]');
  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{ en.target.classList.toggle('on', en.isIntersecting); });
  },{threshold:.15});
  svgs.forEach(s=>io.observe(s));

  const reduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  if(!reduced){
    let ticking=false;
    window.addEventListener('scroll',()=>{
      if(ticking) return; ticking=true;
      requestAnimationFrame(()=>{
        svgs.forEach((s,i)=>{
          const rect=s.getBoundingClientRect();
          const speed = 0.06 + (i%3)*0.03;
          const offset = (window.innerHeight/2 - rect.top - rect.height/2) * speed;
          if(s.classList.contains('on')){
            s.style.transform = `scale(1) translateY(${offset*-1}px)`;
          }
        });
        ticking=false;
      });
    }, {passive:true});
  }
})();

/* ---------------- word-by-word wobble reveal ---------------- */
(function(){
  const reduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  document.querySelectorAll('[data-wobble]').forEach(el=>{
    const original = el.innerHTML;
    // split on spaces while preserving inner <span class="accent"> markup as its own unit
    const wrapper = document.createElement('span');
    wrapper.innerHTML = original;
    const frag = document.createDocumentFragment();
    wrapper.childNodes.forEach(node=>{
      if(node.nodeType===3){ // text node
        node.textContent.split(' ').forEach((w,i,arr)=>{
          if(w==='') return;
          const span=document.createElement('span');
          span.className='wobble-word';
          span.textContent=w;
          frag.appendChild(span);
          if(i<arr.length-1) frag.appendChild(document.createTextNode(' '));
        });
      } else {
        const span=document.createElement('span');
        span.className='wobble-word';
        span.appendChild(node.cloneNode(true));
        frag.appendChild(span);
      }
    });
    el.innerHTML='';
    el.appendChild(frag);
  });

  if(reduced){
    document.querySelectorAll('.wobble-word').forEach(w=>{ w.style.opacity=1; w.style.transform='none'; });
    return;
  }

  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{
      const words=en.target.querySelectorAll('.wobble-word');
      if(en.isIntersecting){
        words.forEach((w,i)=>{ setTimeout(()=>w.classList.add('on'), i*45); });
      } else {
        words.forEach(w=>w.classList.remove('on'));
      }
    });
  },{threshold:.5});
  document.querySelectorAll('[data-wobble]').forEach(el=>io.observe(el));
})();

/* ---------------- hand-drawn underline draw-on-scroll ---------------- */
(function(){
  const heads=document.querySelectorAll('[data-underline]');
  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{
      en.target.querySelector('.heading-underline').classList.toggle('on', en.isIntersecting);
    });
  },{threshold:.6});
  heads.forEach(h=>io.observe(h));
})();

/* ---------------- full-theme shift per section ---------------- */
(function(){
  const themes = {
    hero:    { paper:'#f2ece0', paper2:'#eae2d2', ink:'#2c261e', inkSoft:'#514a3e', clay:'#b8674a', line:'#cfc4ac' },
    intro:   { paper:'#eef0e2', paper2:'#e4e8d4', ink:'#262f1e', inkSoft:'#4a5240', clay:'#6f8a3f', line:'#c7cdb0' },
    work:    { paper:'#f6e7d6', paper2:'#efd9bf', ink:'#3a2716', inkSoft:'#5c4630', clay:'#c1602c', line:'#dcc2a0' },
    about:   { paper:'#f2e4e6', paper2:'#ead4d8', ink:'#33222a', inkSoft:'#5a4148', clay:'#a8566b', line:'#d8bec3' },
    skills:  { paper:'#e4edee', paper2:'#d4e4e6', ink:'#1f2e30', inkSoft:'#3d5254', clay:'#3f7d7a', line:'#b9d2d3' },
    contact: { paper:'#f7edd4', paper2:'#f0e0b6', ink:'#3a2f14', inkSoft:'#5c4e2a', clay:'#b8863c', line:'#e2cf9c' }
  };
  const root=document.documentElement;
  const sections=Object.keys(themes).map(id=>document.getElementById(id)).filter(Boolean);

  function applyTheme(name){
    const t=themes[name]; if(!t) return;
    root.style.setProperty('--paper', t.paper);
    root.style.setProperty('--paper-2', t.paper2);
    root.style.setProperty('--ink', t.ink);
    root.style.setProperty('--ink-soft', t.inkSoft);
    root.style.setProperty('--clay', t.clay);
    root.style.setProperty('--line', t.line);
  }

  const io=new IntersectionObserver(entries=>{
    entries.forEach(en=>{
      if(en.isIntersecting && en.intersectionRatio>0.5){
        applyTheme(en.target.id);
      }
    });
  },{threshold:[0.5]});
  sections.forEach(s=>io.observe(s));
})();

/* ---------------- skills constellation ---------------- */
(function(){
  const canvas=document.getElementById('skills-canvas');
  const ctx=canvas.getContext('2d');
  function resize(){ canvas.width=canvas.clientWidth; canvas.height=canvas.clientHeight; }
  resize(); window.addEventListener('resize',resize);

  const skills=[
    {label:'React',group:'FRONTEND'},{label:'Next.js',group:'FRONTEND'},{label:'TypeScript',group:'FRONTEND'},
    {label:'Three.js',group:'3D'},{label:'WebGL',group:'3D'},{label:'GLSL',group:'3D'},
    {label:'Figma',group:'DESIGN'},{label:'Motion',group:'DESIGN'},
    {label:'Node.js',group:'BACKEND'},{label:'APIs',group:'BACKEND'}
  ];
  const groupColor={FRONTEND:'#b8674a','3D':'#8c5a3c',DESIGN:'#5c6b45',BACKEND:'#8a7f6c'};
  let hoverIdx=-1;

  function layout(){
    const cx=canvas.width/2, cy=canvas.height/2, r=Math.min(cx,cy)*.72;
    skills.forEach((s,i)=>{
      const a = (i/skills.length)*Math.PI*2;
      s.x = cx + Math.cos(a)*r*(0.55+0.45*Math.sin(i*1.7));
      s.y = cy + Math.sin(a)*r*(0.55+0.45*Math.cos(i*1.3));
    });
  }
  layout(); window.addEventListener('resize',layout);

  canvas.addEventListener('mousemove',e=>{
    const rect=canvas.getBoundingClientRect();
    const mx=e.clientX-rect.left, my=e.clientY-rect.top;
    hoverIdx=-1;
    skills.forEach((s,i)=>{ if(Math.hypot(mx-s.x,my-s.y)<26) hoverIdx=i; });
  });

  function draw(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    // connections
    ctx.lineWidth=1;
    for(let i=0;i<skills.length;i++){
      for(let j=i+1;j<skills.length;j++){
        const same = skills[i].group===skills[j].group;
        const active = hoverIdx===i || hoverIdx===j;
        if(same || active){
          ctx.strokeStyle = active? 'rgba(184,103,74,0.7)' : 'rgba(184,103,74,0.16)';
          ctx.beginPath(); ctx.moveTo(skills[i].x,skills[i].y); ctx.lineTo(skills[j].x,skills[j].y); ctx.stroke();
        }
      }
    }
    // nodes
    skills.forEach((s,i)=>{
      const active = hoverIdx===i;
      ctx.beginPath();
      ctx.fillStyle = active? '#b8674a' : (groupColor[s.group]||'#2c261e');
      ctx.globalAlpha = active?1:.85;
      ctx.arc(s.x,s.y, active?7:5, 0, Math.PI*2);
      ctx.fill();
      ctx.globalAlpha=1;
      ctx.font = active? '600 13px Sora, sans-serif' : '400 11px Sora, sans-serif';
      ctx.fillStyle = active? '#2c261e' : '#514a3e';
      ctx.fillText(s.label, s.x+10, s.y+4);
    });
    requestAnimationFrame(draw);
  }
  draw();
})();

/* ---------------- signature moment: particle name reassembly ---------------- */
(function(){
  const logo=document.getElementById('logoBtn');
  const heroName=document.getElementById('heroName');
  function trigger(){
    heroName.style.transition='opacity .25s, transform .25s';
    heroName.style.opacity='0';
    heroName.style.transform='translateY(12px) scale(.98)';
    setTimeout(()=>{
      document.getElementById('hero').scrollIntoView({behavior: window.matchMedia('(prefers-reduced-motion: reduce)').matches?'auto':'smooth'});
      setTimeout(()=>{
        heroName.style.opacity='1';
        heroName.style.transform='translateY(0) scale(1)';
      },350);
    },200);
  }
  logo.addEventListener('click', trigger);
  logo.addEventListener('keydown', e=>{ if(e.key==='Enter'||e.key===' ') trigger(); });
})();
</script>
</body>
</html>
