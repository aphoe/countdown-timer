<script setup>
import { onMounted, onUnmounted, ref } from 'vue'

const canvasRef = ref(null)
let ctx = null
let animationId = null
let particles = []
let fireworks = []

class Particle {
  constructor(x, y, color, velocity, gravity = 0.05, friction = 0.98, size = 3) {
    this.x = x
    this.y = y
    this.color = color
    this.velocity = velocity
    this.gravity = gravity
    this.friction = friction
    this.alpha = 1
    this.decay = Math.random() * 0.015 + 0.01
    this.size = size
  }

  draw() {
    ctx.save()
    ctx.globalAlpha = this.alpha
    ctx.beginPath()
    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2)
    ctx.fillStyle = this.color
    ctx.fill()
    ctx.restore()
  }

  update() {
    this.velocity.x *= this.friction
    this.velocity.y *= this.friction
    this.velocity.y += this.gravity
    this.x += this.velocity.x
    this.y += this.velocity.y
    this.alpha -= this.decay
  }
}

class Firework {
  constructor(x, targetY, color) {
    this.x = x
    this.y = window.innerHeight
    this.targetY = targetY
    this.color = color
    this.velocity = { x: 0, y: -10 - Math.random() * 5 }
    this.exploded = false
    this.size = 4
  }

  draw() {
    ctx.beginPath()
    ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2)
    ctx.fillStyle = this.color
    ctx.fill()
    
    // Trail
    ctx.beginPath()
    ctx.moveTo(this.x, this.y)
    ctx.lineTo(this.x, this.y + 20)
    ctx.strokeStyle = this.color
    ctx.lineWidth = 2
    ctx.stroke()
  }

  update() {
    this.velocity.y += 0.2
    this.y += this.velocity.y
    
    if (this.velocity.y >= 0 || this.y <= this.targetY) {
      this.exploded = true
      this.explode()
    }
  }

  explode() {
    const colors = [
      '#ff0000', '#ff6600', '#ffff00', '#00ff00', 
      '#00ffff', '#0066ff', '#9933ff', '#ff00ff',
      '#7f13ec', '#ff1493', '#ffd700', '#00ff7f'
    ]
    const particleCount = 80 + Math.floor(Math.random() * 50)
    
    for (let i = 0; i < particleCount; i++) {
      const angle = (Math.PI * 2 / particleCount) * i
      const velocity = 3 + Math.random() * 6
      const color = colors[Math.floor(Math.random() * colors.length)]
      
      particles.push(new Particle(
        this.x,
        this.y,
        color,
        {
          x: Math.cos(angle) * velocity,
          y: Math.sin(angle) * velocity
        },
        0.04,
        0.96,
        2 + Math.random() * 2
      ))
    }
    
    // Add sparkle effect
    for (let i = 0; i < 30; i++) {
      const angle = Math.random() * Math.PI * 2
      const velocity = 1 + Math.random() * 3
      
      particles.push(new Particle(
        this.x,
        this.y,
        '#ffffff',
        {
          x: Math.cos(angle) * velocity,
          y: Math.sin(angle) * velocity
        },
        0.02,
        0.98,
        1
      ))
    }
  }
}

const colors = ['#ff0000', '#ffcc00', '#00ff00', '#00ccff', '#ff00ff', '#7f13ec', '#ff6600']

const createFirework = () => {
  const x = Math.random() * window.innerWidth
  const targetY = 100 + Math.random() * (window.innerHeight * 0.4)
  const color = colors[Math.floor(Math.random() * colors.length)]
  fireworks.push(new Firework(x, targetY, color))
}

const animate = () => {
  ctx.fillStyle = 'rgba(20, 17, 24, 0.2)'
  ctx.fillRect(0, 0, window.innerWidth, window.innerHeight)
  
  // Update and draw fireworks
  fireworks = fireworks.filter(firework => {
    firework.draw()
    firework.update()
    return !firework.exploded
  })
  
  // Update and draw particles
  particles = particles.filter(particle => {
    if (particle.alpha > 0) {
      particle.draw()
      particle.update()
      return true
    }
    return false
  })
  
  // Randomly create new fireworks
  if (Math.random() < 0.08) {
    createFirework()
  }
  
  animationId = requestAnimationFrame(animate)
}

const handleResize = () => {
  if (canvasRef.value) {
    canvasRef.value.width = window.innerWidth
    canvasRef.value.height = window.innerHeight
  }
}

onMounted(() => {
  const canvas = canvasRef.value
  ctx = canvas.getContext('2d')
  canvas.width = window.innerWidth
  canvas.height = window.innerHeight
  
  window.addEventListener('resize', handleResize)
  
  // Create initial fireworks
  for (let i = 0; i < 5; i++) {
    setTimeout(createFirework, i * 200)
  }
  
  animate()
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  if (animationId) {
    cancelAnimationFrame(animationId)
  }
})
</script>

<template>
  <div class="fixed inset-0 z-50">
    <canvas ref="canvasRef" class="absolute inset-0"></canvas>
    
    <!-- Celebration Text Overlay -->
    <div class="absolute inset-0 flex flex-col items-center justify-center pointer-events-none">
      <div class="text-center space-y-6 animate-pulse-text">
        <h1 class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-bold text-white celebration-text font-display tracking-tighter">
          Happy New Year!
        </h1>
        <p class="text-3xl sm:text-4xl md:text-5xl font-semibold text-white/90 celebration-subtext font-display">
          Welcome to 2026
        </p>
        <div class="text-6xl mt-8">
          🎉🎊🥳🎆✨
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
canvas {
  background: transparent;
}

.celebration-text {
  text-shadow: 
    0 0 20px rgba(127, 19, 236, 0.8),
    0 0 40px rgba(127, 19, 236, 0.6),
    0 0 60px rgba(127, 19, 236, 0.4),
    0 0 80px rgba(255, 255, 255, 0.3),
    2px 2px 4px rgba(0, 0, 0, 0.5);
}

.celebration-subtext {
  text-shadow: 
    0 0 15px rgba(127, 19, 236, 0.6),
    0 0 30px rgba(127, 19, 236, 0.4),
    2px 2px 4px rgba(0, 0, 0, 0.5);
}

@keyframes pulse-text {
  0%, 100% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.02);
    opacity: 0.95;
  }
}

.animate-pulse-text {
  animation: pulse-text 3s ease-in-out infinite;
}
</style>
