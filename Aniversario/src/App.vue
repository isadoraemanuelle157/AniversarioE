<template>
  <div class="birthday-page">
    <!-- Partículas de fundo -->
    <div class="particles">
      <span v-for="n in 20" :key="n" class="particle" :style="getParticleStyle(n)">🎈</span>
    </div>

    <div class="card" :class="{ 'celebrate': isToday }">
      <!-- Badge animado -->
      <div class="badge" v-if="!isToday">
        <span class="pulse"></span>
        <span class="text">Em breve</span>
      </div>

      <div class="header">
        <div class="icon-wrapper">
          <span class="party-icon">🎉</span>
          <div class="ripple"></div>
        </div>
        <h1 class="title">
          <span v-if="!isToday">Aniversário da Tuti</span>
          <span v-else>É Hoje!</span>
        </h1>
        <p class="subtitle">{{ isToday ? 'Momento especial chegou!' : 'Um dia muito foda está chegando' }}</p>
      </div>

      <!-- Data destacada -->
      <div class="date-section" v-if="!isToday">
        <div class="date-card">
          <span class="day">18</span>
          <div class="divider"></div>
          <div class="month-year">
            <span class="month">Maio</span>
           
          </div>
        </div>
      </div>

      <!-- Contador -->
      <div class="countdown" v-if="!isToday">
        <div 
          v-for="(unit, index) in timeUnits" 
          :key="unit.label"
          class="time-box"
          :class="{ 'flip': lastUpdate === index }"
        >
          <div class="number-wrapper">
            <span class="number">{{ formatNumber(unit.value) }}</span>
            <span class="shadow">{{ formatNumber(unit.value) }}</span>
          </div>
          <span class="label">{{ unit.label }}</span>
          <div class="progress-bar" :style="{ width: unit.progress + '%' }"></div>
        </div>
      </div>

      <!-- Mensagem final -->
      <div class="final-message" v-if="isToday">
        <div class="cake">🎂</div>
        <p class="celebrate-text">Feliz Aniversário!</p>
        <button class="btn-celebrate" @click="launchConfetti">
          Celebrar! 🎊
        </button>
      </div>

      <div class="footer" v-if="!isToday">
        <p class="message">Se prepare para celebrar momentos inesquecíveis!!!</p>
        <div class="hearts">
          
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BirthdayCountdown',
  
  data() {
    return {
      targetDate: new Date('2026-05-18T00:00:00'),
      now: new Date(),
      lastUpdate: null,
      isToday: false,
      timeUnits: [
        { label: 'Meses', value: 0, max: 12, progress: 0 },
        { label: 'Dias', value: 0, max: 30, progress: 0 },
        { label: 'Horas', value: 0, max: 24, progress: 0 },
        { label: 'Minutos', value: 0, max: 60, progress: 0 },
        { label: 'Segundos', value: 0, max: 60, progress: 0 }
      ]
    }
  },

  mounted() {
    this.updateCountdown()
    this.timer = setInterval(this.updateCountdown, 1000)
  },

  beforeUnmount() {
    clearInterval(this.timer)
  },

  methods: {
    updateCountdown() {
      this.now = new Date()
      const diff = this.targetDate - this.now

      // Verifica se é hoje
      const today = new Date()
      const target = new Date('2026-05-18')
      this.isToday = today.toDateString() === target.toDateString()

      if (diff <= 0 && !this.isToday) {
        this.timeUnits.forEach(unit => unit.value = 0)
        return
      }

      // Cálculos precisos
      const totalSeconds = Math.floor(diff / 1000)
      
      // Cálculo de meses considerando dias variáveis
      let remaining = new Date(this.now)
      let months = 0
      while (true) {
        const nextMonth = new Date(remaining)
        nextMonth.setMonth(nextMonth.getMonth() + 1)
        if (nextMonth > this.targetDate) break
        remaining = nextMonth
        months++
      }

      const daysDiff = Math.floor((this.targetDate - remaining) / (1000 * 60 * 60 * 24))
      const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60))
      const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
      const seconds = Math.floor((diff % (1000 * 60)) / 1000)

      // Atualiza valores com animação
      const values = [months, daysDiff, hours, minutes, seconds]
      const maxValues = [12, 30, 24, 60, 60]
      
      values.forEach((val, index) => {
        if (this.timeUnits[index].value !== val) {
          this.lastUpdate = index
          setTimeout(() => this.lastUpdate = null, 600)
        }
        this.timeUnits[index].value = val
        this.timeUnits[index].progress = (val / maxValues[index]) * 100
      })
    },

    formatNumber(num) {
      return num.toString().padStart(2, '0')
    },

    getParticleStyle(n) {
      return {
        left: `${Math.random() * 100}%`,
        animationDelay: `${Math.random() * 20}s`,
        animationDuration: `${15 + Math.random() * 10}s`,
        fontSize: `${20 + Math.random() * 20}px`
      }
    },

    launchConfetti() {
      // Simulação de confete com CSS
      const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#f9ca24', '#f0932b']
      for (let i = 0; i < 50; i++) {
        setTimeout(() => {
          const el = document.createElement('div')
          el.style.cssText = `
            position: fixed;
            width: 10px;
            height: 10px;
            background: ${colors[Math.floor(Math.random() * colors.length)]};
            left: ${Math.random() * 100}vw;
            top: -10px;
            border-radius: 50%;
            animation: confetti-fall 3s linear forwards;
            z-index: 9999;
          `
          document.body.appendChild(el)
          setTimeout(() => el.remove(), 3000)
        }, i * 50)
      }
    }
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700;800&display=swap');

.birthday-page {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  font-family: 'Poppins', sans-serif;
  position: relative;
  overflow: hidden;
  padding: 20px;
}

/* Partículas animadas */
.particles {
  position: absolute;
  width: 100%;
  height: 100%;
  overflow: hidden;
  pointer-events: none;
}

.particle {
  position: absolute;
  bottom: -50px;
  opacity: 0.6;
  animation: float-up linear infinite;
}

@keyframes float-up {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 0.6;
  }
  90% {
    opacity: 0.6;
  }
  100% {
    transform: translateY(-100vh) rotate(360deg);
    opacity: 0;
  }
}

/* Card principal */
.card {
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  padding: 50px 40px;
  border-radius: 30px;
  text-align: center;
  box-shadow: 
    0 25px 50px -12px rgba(0, 0, 0, 0.25),
    0 0 0 1px rgba(255, 255, 255, 0.1);
  max-width: 900px;
  width: 100%;
  position: relative;
  z-index: 10;
  animation: card-entrance 0.8s ease-out;
  border: 1px solid rgba(255, 255, 255, 0.2);
}

@keyframes card-entrance {
  from {
    opacity: 0;
    transform: translateY(30px) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.card.celebrate {
  background: linear-gradient(135deg, #ffeaa7 0%, #fab1a0 100%);
  animation: celebrate-pulse 2s infinite;
}

@keyframes celebrate-pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.02); }
}

/* Badge */
.badge {
  position: absolute;
  top: -15px;
  right: 30px;
  background: #ff6b6b;
  color: white;
  padding: 8px 20px;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1px;
  display: flex;
  align-items: center;
  gap: 8px;
  box-shadow: 0 4px 15px rgba(255, 107, 107, 0.4);
}

.pulse {
  width: 8px;
  height: 8px;
  background: white;
  border-radius: 50%;
  animation: pulse-dot 2s infinite;
}

@keyframes pulse-dot {
  0%, 100% { opacity: 1; transform: scale(1); }
  50% { opacity: 0.5; transform: scale(1.2); }
}

/* Header */
.header {
  margin-bottom: 40px;
}

.icon-wrapper {
  position: relative;
  display: inline-block;
  margin-bottom: 20px;
}

.party-icon {
  font-size: 60px;
  display: block;
  animation: bounce 2s infinite;
  filter: drop-shadow(0 4px 8px rgba(0,0,0,0.1));
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

.ripple {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 80px;
  height: 80px;
  border: 2px solid rgba(255, 107, 107, 0.3);
  border-radius: 50%;
  animation: ripple 2s infinite;
}

@keyframes ripple {
  0% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
  100% { transform: translate(-50%, -50%) scale(1.5); opacity: 0; }
}

.title {
  font-size: 48px;
  font-weight: 800;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin: 0;
  line-height: 1.2;
}

.subtitle {
  color: #666;
  font-size: 18px;
  margin-top: 10px;
  font-weight: 400;
}

/* Data */
.date-section {
  margin: 30px 0;
}

.date-card {
  display: inline-flex;
  align-items: center;
  gap: 15px;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  padding: 20px 35px;
  border-radius: 20px;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.05);
}

.day {
  font-size: 48px;
  font-weight: 800;
  color: #667eea;
  line-height: 1;
}

.divider {
  width: 2px;
  height: 40px;
  background: #dee2e6;
}

.month-year {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  text-align: left;
}

.month {
  font-size: 20px;
  font-weight: 600;
  color: #495057;
  text-transform: uppercase;
  letter-spacing: 1px;
}

.year {
  font-size: 16px;
  color: #adb5bd;
  font-weight: 500;
}

/* Contador */
.countdown {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin: 40px 0;
  flex-wrap: wrap;
}

.time-box {
  background: linear-gradient(145deg, #2d3436 0%, #000000 100%);
  color: white;
  padding: 25px 20px;
  border-radius: 20px;
  min-width: 100px;
  position: relative;
  overflow: hidden;
  box-shadow: 
    0 10px 20px rgba(0,0,0,0.2),
    inset 0 1px 0 rgba(255,255,255,0.1);
  transition: transform 0.3s ease;
}

.time-box:hover {
  transform: translateY(-5px);
}

.time-box.flip {
  animation: flip-number 0.6s ease;
}

@keyframes flip-number {
  0% { transform: rotateX(0); }
  50% { transform: rotateX(90deg); }
  100% { transform: rotateX(0); }
}

.number-wrapper {
  position: relative;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.number {
  font-size: 36px;
  font-weight: 700;
  font-family: 'Courier New', monospace;
  z-index: 2;
  text-shadow: 0 2px 4px rgba(0,0,0,0.3);
}

.shadow {
  position: absolute;
  font-size: 36px;
  font-weight: 700;
  opacity: 0.1;
  transform: translateY(3px);
  z-index: 1;
}

.label {
  display: block;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 2px;
  margin-top: 8px;
  opacity: 0.8;
  font-weight: 500;
}

.progress-bar {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 3px;
  background: linear-gradient(90deg, #667eea, #764ba2);
  transition: width 1s ease;
}

/* Mensagem final */
.final-message {
  padding: 40px 0;
}

.cake {
  font-size: 80px;
  animation: cake-bounce 1s infinite;
  display: inline-block;
}

@keyframes cake-bounce {
  0%, 100% { transform: scale(1) rotate(0deg); }
  25% { transform: scale(1.1) rotate(-5deg); }
  75% { transform: scale(1.1) rotate(5deg); }
}

.celebrate-text {
  font-size: 36px;
  font-weight: 700;
  color: #e17055;
  margin: 20px 0;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
}

.btn-celebrate {
  background: linear-gradient(135deg, #ff6b6b 0%, #ee5a6f 100%);
  color: white;
  border: none;
  padding: 15px 40px;
  font-size: 18px;
  font-weight: 600;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 10px 30px rgba(238, 90, 111, 0.4);
  transition: all 0.3s ease;
  font-family: 'Poppins', sans-serif;
}

.btn-celebrate:hover {
  transform: translateY(-3px);
  box-shadow: 0 15px 40px rgba(238, 90, 111, 0.5);
}

.btn-celebrate:active {
  transform: translateY(0);
}

/* Footer */
.footer {
  margin-top: 40px;
  padding-top: 30px;
  border-top: 1px solid rgba(0,0,0,0.05);
}

.message {
  font-size: 16px;
  color: #666;
  font-style: italic;
  margin-bottom: 15px;
}

.hearts {
  display: flex;
  justify-content: center;
  gap: 10px;
}

.heart {
  font-size: 20px;
  animation: heartbeat 1.5s infinite;
}

.heart:nth-child(2) { animation-delay: 0.2s; }
.heart:nth-child(3) { animation-delay: 0.4s; }

@keyframes heartbeat {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.2); }
}

/* Animação confete */
@keyframes confetti-fall {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
  }
  100% {
    transform: translateY(100vh) rotate(720deg);
    opacity: 0;
  }
}

/* Responsivo */
@media (max-width: 768px) {
  .card {
    padding: 30px 20px;
    margin: 10px;
  }

  .title {
    font-size: 32px;
  }

  .countdown {
    gap: 10px;
  }

  .time-box {
    min-width: 70px;
    padding: 20px 15px;
  }

  .number {
    font-size: 28px;
  }

  .date-card {
    padding: 15px 25px;
  }

  .day {
    font-size: 36px;
  }
}

@media (max-width: 480px) {
  .time-box {
    min-width: 60px;
    padding: 15px 10px;
  }

  .number {
    font-size: 24px;
  }

  .label {
    font-size: 10px;
  }
}
</style>