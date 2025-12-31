<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'
import Fireworks from './components/Fireworks.vue'

// Get user's timezone and set target to midnight January 1, 2026 in their local time
//const userTimezone = Intl.DateTimeFormat().resolvedOptions().timeZone
const userTimezone = "America/Chicago"
const targetDate = new Date(new Date('2026-01-01T00:00:00').toLocaleString('en-US', { timeZone: userTimezone })).getTime()
console.log(targetDate);

const now = ref(Date.now())
const isNewYear = ref(false)
const isDarkMode = ref(true)

let intervalId = null

const timeLeft = computed(() => {
  const diff = targetDate - now.value
  
  if (diff <= 0) {
    isNewYear.value = true
    return { days: 0, hours: 0, minutes: 0, seconds: 0 }
  }
  
  const days = Math.floor(diff / (1000 * 60 * 60 * 24))
  const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
  const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
  const seconds = Math.floor((diff % (1000 * 60)) / 1000)
  
  return { days, hours, minutes, seconds }
})

const padNumber = (num) => String(num).padStart(2, '0')

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value
  document.documentElement.classList.toggle('dark', isDarkMode.value)
}

onMounted(() => {
  intervalId = setInterval(() => {
    now.value = Date.now()
  }, 1000)
})

onUnmounted(() => {
  if (intervalId) clearInterval(intervalId)
})
</script>

<template>
  <div :class="['fixed inset-0 flex flex-col overflow-hidden transition-colors duration-300', isDarkMode ? 'bg-background-dark text-white' : 'bg-background-light text-slate-900']">
    <!-- Fireworks overlay -->
    <Fireworks v-if="isNewYear" />
    
    <!-- Background Gradient Mesh -->
    <div class="fixed inset-0 pointer-events-none bg-gradient-mesh z-0"></div>
    
    <!-- Navigation -->
    <nav class="relative z-20 px-6 py-4 flex justify-between items-center border-b backdrop-blur-md"
         :class="isDarkMode ? 'border-white/10 bg-background-dark/80' : 'border-gray-200 bg-background-light/80'">
      <div class="flex items-center gap-3">
        <div class="text-primary animate-pulse-slow">
          <span class="material-symbols-outlined text-3xl">celebration</span>
        </div>
        <h1 class="text-xl font-bold tracking-tight font-display">NYE 2026</h1>
      </div>
      <button @click="toggleDarkMode"
              class="flex items-center justify-center p-2 rounded-lg transition-colors bg-transparent text-white hover:text-primary">
        <span class="material-symbols-outlined text-[20px]">{{ isDarkMode ? 'light_mode' : 'dark_mode' }}</span>
      </button>
    </nav>

    <!-- Full Screen Centered Content -->
    <main class="relative z-10 flex-1 flex flex-col items-center justify-center w-full h-full px-4 sm:px-6 lg:px-8 font-display">
      <!-- Hero Text -->
      <div class="text-center mb-8 md:mb-12 space-y-4">
        <div class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-primary/10 border border-primary/20 text-primary text-xs md:text-sm font-bold uppercase tracking-widest">
          <span class="w-2 h-2 rounded-full bg-primary animate-pulse"></span>
          {{ isNewYear ? '🎊 Celebration Time! 🎊' : 'Official Countdown' }}
        </div>
        <h2 class="text-5xl md:text-7xl lg:text-8xl xl:text-9xl font-bold tracking-tighter"
            :class="isDarkMode ? 'text-white' : 'text-slate-900'">
          <template v-if="isNewYear">
            <span class="text-primary text-glow">Happy New Year!</span>
          </template>
          <template v-else>
            NEW YEAR <span class="text-primary text-glow">2026</span>
          </template>
        </h2>
        <p :class="isDarkMode ? 'text-slate-400' : 'text-slate-500'" class="max-w-2xl mx-auto text-lg md:text-xl">
          {{ isNewYear ? 'Welcome to 2026! May this year bring you joy, success, and endless possibilities.' : 'The future is approaching. Join us as we count down the seconds to a new beginning.' }}
        </p>
      </div>

      <!-- Full Width Timer Grid -->
      <div class="w-full max-w-7xl px-4">
        <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 md:gap-6 lg:gap-8">
          <!-- Days -->
          <div class="timer-card group relative flex flex-col items-center justify-center aspect-square md:aspect-[4/3] p-4 md:p-8 rounded-3xl shadow-2xl transition-all duration-300 box-glow"
               :class="isDarkMode ? 'bg-surface-dark border border-white/5 hover:border-primary/50' : 'bg-white border border-gray-200 hover:border-primary/50'">
            <div class="absolute inset-0 bg-gradient-to-b from-primary/5 to-transparent rounded-3xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
            <span class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-bold tabular-nums tracking-tight group-hover:text-primary transition-colors duration-300"
                  :class="isDarkMode ? 'text-white' : 'text-slate-900'">
              {{ timeLeft.days }}
            </span>
            <span class="mt-2 md:mt-4 text-sm md:text-lg font-semibold uppercase tracking-[0.25em]"
                  :class="isDarkMode ? 'text-slate-500' : 'text-slate-400'">Days</span>
          </div>

          <!-- Hours -->
          <div class="timer-card group relative flex flex-col items-center justify-center aspect-square md:aspect-[4/3] p-4 md:p-8 rounded-3xl shadow-2xl transition-all duration-300 box-glow"
               :class="isDarkMode ? 'bg-surface-dark border border-white/5 hover:border-primary/50' : 'bg-white border border-gray-200 hover:border-primary/50'">
            <div class="absolute inset-0 bg-gradient-to-b from-primary/5 to-transparent rounded-3xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
            <span class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-bold tabular-nums tracking-tight group-hover:text-primary transition-colors duration-300"
                  :class="isDarkMode ? 'text-white' : 'text-slate-900'">
              {{ padNumber(timeLeft.hours) }}
            </span>
            <span class="mt-2 md:mt-4 text-sm md:text-lg font-semibold uppercase tracking-[0.25em]"
                  :class="isDarkMode ? 'text-slate-500' : 'text-slate-400'">Hours</span>
          </div>

          <!-- Minutes -->
          <div class="timer-card group relative flex flex-col items-center justify-center aspect-square md:aspect-[4/3] p-4 md:p-8 rounded-3xl shadow-2xl transition-all duration-300 box-glow"
               :class="isDarkMode ? 'bg-surface-dark border border-white/5 hover:border-primary/50' : 'bg-white border border-gray-200 hover:border-primary/50'">
            <div class="absolute inset-0 bg-gradient-to-b from-primary/5 to-transparent rounded-3xl opacity-0 group-hover:opacity-100 transition-opacity"></div>
            <span class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-bold tabular-nums tracking-tight group-hover:text-primary transition-colors duration-300"
                  :class="isDarkMode ? 'text-white' : 'text-slate-900'">
              {{ padNumber(timeLeft.minutes) }}
            </span>
            <span class="mt-2 md:mt-4 text-sm md:text-lg font-semibold uppercase tracking-[0.25em]"
                  :class="isDarkMode ? 'text-slate-500' : 'text-slate-400'">Minutes</span>
          </div>

          <!-- Seconds -->
          <div class="timer-card group relative flex flex-col items-center justify-center aspect-square md:aspect-[4/3] p-4 md:p-8 rounded-3xl shadow-2xl transition-all duration-300 box-glow seconds-glow"
               :class="isDarkMode ? 'bg-surface-dark border-2 border-primary/30 hover:border-primary/70' : 'bg-white border-2 border-primary/30 hover:border-primary/70'">
            <div class="absolute inset-0 bg-gradient-to-b from-primary/10 to-transparent rounded-3xl"></div>
            <span class="text-6xl sm:text-7xl md:text-8xl lg:text-9xl font-bold text-primary text-glow tabular-nums tracking-tight">
              {{ padNumber(timeLeft.seconds) }}
            </span>
            <span class="mt-2 md:mt-4 text-sm md:text-lg font-semibold uppercase tracking-[0.25em] text-primary">Seconds</span>
          </div>
        </div>
      </div>

      <!-- Celebrate Button (when it's New Year) -->
      <div v-if="isNewYear" class="mt-10 md:mt-16 flex flex-col items-center gap-4">
        <button @click="isNewYear = false; setTimeout(() => isNewYear = true, 100)"
                class="group relative flex items-center justify-center gap-3 px-10 py-4 bg-primary text-white rounded-xl font-bold text-base tracking-wide overflow-hidden transition-transform active:scale-95 hover:shadow-xl hover:shadow-primary/50">
          <span class="material-symbols-outlined text-2xl">celebration</span>
          <span>MORE FIREWORKS!</span>
        </button>
      </div>
    </main>

    <!-- Minimal Footer -->
    <footer class="relative z-10 py-4 px-6 text-center"
            :class="isDarkMode ? 'border-t border-white/5' : 'border-t border-gray-200'">
      <p :class="isDarkMode ? 'text-slate-600' : 'text-slate-500'" class="text-xs">© 2025 Countdown Co. All rights reserved.</p>
    </footer>
  </div>
</template>

<style>
/* Custom glow effects for the cyber-festive theme */
.text-glow {
  text-shadow: 0 0 30px rgba(127, 19, 236, 0.6), 0 0 60px rgba(127, 19, 236, 0.3);
}
.box-glow {
  box-shadow: 0 0 40px -10px rgba(127, 19, 236, 0.3);
}
.seconds-glow {
  box-shadow: 0 0 60px -10px rgba(127, 19, 236, 0.5), 0 0 100px -20px rgba(127, 19, 236, 0.3);
  animation: pulse-glow 2s ease-in-out infinite;
}
@keyframes pulse-glow {
  0%, 100% {
    box-shadow: 0 0 60px -10px rgba(127, 19, 236, 0.5), 0 0 100px -20px rgba(127, 19, 236, 0.3);
  }
  50% {
    box-shadow: 0 0 80px -10px rgba(127, 19, 236, 0.7), 0 0 120px -20px rgba(127, 19, 236, 0.4);
  }
}
.bg-gradient-mesh {
  background: radial-gradient(circle at 50% 0%, rgba(127, 19, 236, 0.2) 0%, transparent 50%),
              radial-gradient(circle at 0% 100%, rgba(127, 19, 236, 0.15) 0%, transparent 50%),
              radial-gradient(circle at 100% 100%, rgba(127, 19, 236, 0.15) 0%, transparent 50%);
}
</style>
