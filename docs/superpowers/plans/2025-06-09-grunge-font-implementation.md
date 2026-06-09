# Grunge Font Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development or superpowers:executing-plans

**Goal:** Modify index.html to make the "MINACCE" text look grunge/destroyed via enhanced SVG filters, JS path distortion, and destruction masks.

**Architecture:** Single-file modification to index.html — enhance the existing SVG filter, add JavaScript for path vertex jitter and dynamic destruction masks, add noise texture overlay.

**Tech Stack:** HTML, SVG, Vanilla JS, Hydra (existing)

---

### Task 1: SVG Filter Enhancement

**Files:**
- Modify: `index.html:59-69`

- [ ] **Step 1: Replace the noisy-text filter with a more aggressive version**

Replace the existing `#noisy-text` filter with:
```svg
<filter id="noisy-text" x="-20%" y="-20%" width="140%" height="140%">
  <feTurbulence type="fractalNoise" baseFrequency="4" numOctaves="4" seed="1" result="noise"/>
  <feDisplacementMap in="SourceGraphic" in2="noise" scale="28" xChannelSelector="R" yChannelSelector="G" result="displaced"/>
  <feMorphology operator="dilate" radius="0.5" in="displaced" result="eroded"/>
  <feColorMatrix type="matrix" values="
    0.4 0 0 0 0
    0.4 0 0 0 0
    0.4 0 0 0 0
    0.6 0 0 0 0" in="noise" result="darkNoise"/>
  <feComposite operator="in" in="darkNoise" in2="eroded" result="onText"/>
  <feBlend mode="multiply" in="onText" in2="eroded"/>
</filter>
```

- [ ] **Step 2: Add a second turbulence layer for edge roughness**

Add before `</defs>`:
```svg
<filter id="rough-edges" x="-10%" y="-10%" width="120%" height="120%">
  <feTurbulence type="fractalNoise" baseFrequency="0.5" numOctaves="2" seed="2" result="noise"/>
  <feDisplacementMap in="SourceGraphic" in2="noise" scale="8" xChannelSelector="R" yChannelSelector="G"/>
</filter>
```

- [ ] **Step 3: Apply both filters to logo-paths**

Change `filter="url(#noisy-text)"` to `filter="url(#noisy-text) url(#rough-edges)"`

---

### Task 2: JavaScript Path Distortion

**Files:**
- Modify: `index.html` (add script before closing `</body>`)

- [ ] **Step 1: Add path distortion script**

Add after the existing script block:
```js
// Path distortion for grunge effect
(function() {
  const logoPaths = document.getElementById('logo-paths');
  const originalPaths = [];
  
  // Store original path data
  logoPaths.querySelectorAll('path').forEach(function(path) {
    originalPaths.push(path.getAttribute('d'));
  });

  function jitterPath(d, intensity) {
    // Replace numbers in path data with jittered versions
    return d.replace(/-?\d+\.?\d*/g, function(match) {
      if (Math.random() < 0.3) {
        return (parseFloat(match) + (Math.random() - 0.5) * intensity).toFixed(1);
      }
      return match;
    });
  }

  let distortion = 0;
  setInterval(function() {
    distortion = 4 + Math.random() * 6;
    logoPaths.querySelectorAll('path').forEach(function(path, i) {
      path.setAttribute('d', jitterPath(originalPaths[i], distortion));
    });
  }, 4000);
})();
```

---

### Task 3: Destruction Masks

**Files:**
- Modify: `index.html` — add mask logic

- [ ] **Step 1: Add destruction mask SVG def**

Add after rough-edges filter in `<defs>`:
```svg
<mask id="destruction-mask">
  <rect width="1366" height="768" fill="white"/>
</mask>
```

- [ ] **Step 2: Add mask update script**

Add to the path distortion script block:
```js
// Destruction mask — random chunks
(function() {
  const ns = 'http://www.w3.org/2000/svg';
  const mask = document.querySelector('#destruction-mask');

  function updateMask() {
    while (mask.firstChild) mask.removeChild(mask.firstChild);
    const bg = document.createElementNS(ns, 'rect');
    bg.setAttribute('width', '1366');
    bg.setAttribute('height', '768');
    bg.setAttribute('fill', 'white');
    mask.appendChild(bg);

    const chunkCount = 3 + Math.floor(Math.random() * 5);
    for (var i = 0; i < chunkCount; i++) {
      var rect = document.createElementNS(ns, 'rect');
      var x = Math.random() * 1366;
      var y = Math.random() * 768;
      var w = 20 + Math.random() * 80;
      var h = 20 + Math.random() * 80;
      rect.setAttribute('x', x);
      rect.setAttribute('y', y);
      rect.setAttribute('width', w);
      rect.setAttribute('height', h);
      rect.setAttribute('fill', 'black');
      rect.setAttribute('transform', 'rotate(' + (Math.random() - 0.5) * 30 + ' ' + (x + w/2) + ' ' + (y + h/2) + ')');
      mask.appendChild(rect);
    }
  }

  updateMask();
  setInterval(updateMask, 5000);
})();
```

- [ ] **Step 3: Apply mask to logo-paths**

Change `<g id="logo-paths" ...>` to include the mask:
```svg
<g id="logo-paths" filter="url(#noisy-text)" mask="url(#destruction-mask)">
```

---

### Task 4: Texture & Sporco Overlay

**Files:**
- Modify: `index.html` — add noise canvas overlay

- [ ] **Step 1: Add noise overlay canvas after logo**

Add before `</body>`:
```html
<canvas id="noise-overlay" style="position:fixed;top:0;left:0;width:100vw;height:100vh;pointer-events:none;z-index:4;mix-blend-mode:multiply;opacity:0.15;"></canvas>
```

- [ ] **Step 2: Add noise generation script**

Add to the scripts section:
```js
// Noise overlay texture
(function() {
  var canvas = document.getElementById('noise-overlay');
  var ctx = canvas.getContext('2d');
  
  function resize() {
    canvas.width = window.innerWidth * devicePixelRatio;
    canvas.height = window.innerHeight * devicePixelRatio;
  }
  resize();
  window.addEventListener('resize', resize);

  function renderNoise() {
    var w = canvas.width, h = canvas.height;
    var imageData = ctx.createImageData(w, h);
    var data = imageData.data;
    for (var i = 0; i < data.length; i += 4) {
      var val = Math.random() * 255;
      data[i] = val;
      data[i + 1] = val;
      data[i + 2] = val;
      data[i + 3] = 255;
    }
    ctx.putImageData(imageData, 0, 0);
    setTimeout(renderNoise, 200);
  }
  renderNoise();
})();
```

---

### Verification
- Open `index.html` in a browser
- Text should look visibly distressed/grunge: jagged edges, missing chunks, noise texture
- Lightning, Hydra effects, and glitch on hover should still work
- Refresh to confirm persistence
