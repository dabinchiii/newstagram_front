<template>
    <canvas ref="canvas" class="snow-canvas"></canvas>
  </template>
  <script setup>
    import { onMounted, onBeforeUnmount, ref } from "vue";
    
    const canvas = ref(null);
    let ctx;
    let W, H;
    let particles = [];
    const mp = 40;
    let angle = 0;
    let intervalId;
    
    function resizeCanvas() {
      W = canvas.value.width = window.innerWidth;
      H = canvas.value.height = window.innerHeight;
    }
    
    function initParticles() {
      particles = [];
      for (let i = 0; i < mp; i++) {
        particles.push({
          x: Math.random() * W,
          y: Math.random() * H,
          r: Math.random() * 2 + 1,
          d: Math.random() * mp,
        });
      }
    }
    
    function update() {
      angle += 0.01;
    
      for (let i = 0; i < mp; i++) {
        const p = particles[i];
    
        p.y += Math.cos(angle + p.d) + 1 + p.r / 2;
        p.x += Math.sin(angle) * 2;
    
        if (p.x > W + 5 || p.x < -5 || p.y > H) {
          if (i % 3 > 0) {
            particles[i] = {
              x: Math.random() * W,
              y: -10,
              r: p.r,
              d: p.d,
            };
          } else {
            particles[i] = {
              x: Math.sin(angle) > 0 ? -5 : W + 5,
              y: Math.random() * H,
              r: p.r,
              d: p.d,
            };
          }
        }
      }
    }
    
    function draw() {
      ctx.clearRect(0, 0, W, H);
      ctx.fillStyle = "rgba(255,255,255,0.9)";
      ctx.beginPath();
    
      for (const p of particles) {
        ctx.moveTo(p.x, p.y);
        ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2, true);
      }
    
      ctx.fill();
      update();
    }
    
    onMounted(() => {
      ctx = canvas.value.getContext("2d");
      resizeCanvas();
      initParticles();
    
      intervalId = setInterval(draw, 33); // 🔥 원본과 동일
      window.addEventListener("resize", resizeCanvas);
    });
    
    onBeforeUnmount(() => {
      clearInterval(intervalId);
      window.removeEventListener("resize", resizeCanvas);
    });
    </script>
    