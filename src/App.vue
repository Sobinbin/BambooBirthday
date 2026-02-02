<template>
  <div class="birthday-container">
    <div class="cake-container">
      <div class="cake">
        <div class="candle" v-for="n in 5" :key="n">
          <div class="flame" :class="{ 'blowing': isBlowing }"></div>
        </div>
        <div class="cake-layer layer1"></div>
        <div class="cake-layer layer2"></div>
        <div class="cake-layer layer3"></div>
      </div>
    </div>
    
    <div class="message-container">
      <h1 class="birthday-title" :class="{ 'show': showTitle }">
        {{ birthdayMessage }}
      </h1>
      <p class="birthday-subtitle" :class="{ 'show': showSubtitle }">
        {{ subtitle }}
      </p>
      <button @click="blowCandles" class="blow-btn" v-if="!isBlowing">
        🎂 吹蜡烛
      </button>
      <p class="wish-text" v-if="isBlowing">
        {{ wish }}
      </p>
    </div>
    
    <div class="confetti" v-for="(confetti, index) in confettiPieces" 
         :key="index"
         :style="confetti.style">
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const birthdayMessage = ref('生日快乐！')
const subtitle = ref('祝你天天开心，万事如意！')
const wish = ref('愿你的所有愿望都能实现！✨')
const showTitle = ref(false)
const showSubtitle = ref(false)
const isBlowing = ref(false)
const confettiPieces = ref([])

onMounted(() => {
  setTimeout(() => {
    showTitle.value = true
  }, 500)
  setTimeout(() => {
    showSubtitle.value = true
  }, 1000)
})

const blowCandles = () => {
  isBlowing.value = true
  createConfetti()
}

const createConfetti = () => {
  const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#f9ca24', '#6c5ce7', '#fd79a8']
  for (let i = 0; i < 50; i++) {
    const confetti = {
      style: {
        left: Math.random() * 100 + '%',
        top: '-10px',
        backgroundColor: colors[Math.floor(Math.random() * colors.length)],
        animationDelay: Math.random() * 2 + 's',
        transform: `rotate(${Math.random() * 360}deg)`
      }
    }
    confettiPieces.value.push(confetti)
  }
}
</script>

<style scoped>
.birthday-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  position: relative;
  overflow: hidden;
}

.cake-container {
  margin-bottom: 40px;
}

.cake {
  position: relative;
  width: 200px;
  height: 180px;
}

.candle {
  position: absolute;
  width: 8px;
  height: 40px;
  background: linear-gradient(to bottom, #f39c12, #e74c3c);
  border-radius: 2px;
  top: -30px;
}

.candle:nth-child(1) { left: 40px; }
.candle:nth-child(2) { left: 70px; }
.candle:nth-child(3) { left: 96px; }
.candle:nth-child(4) { left: 122px; }
.candle:nth-child(5) { left: 152px; }

.flame {
  position: absolute;
  width: 12px;
  height: 20px;
  background: linear-gradient(to top, #f39c12, #f1c40f, #fff);
  border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
  top: -18px;
  left: -2px;
  animation: flicker 0.5s infinite alternate;
  box-shadow: 0 0 10px #f39c12, 0 0 20px #f1c40f;
}

.flame.blowing {
  animation: blowOut 0.5s forwards;
}

@keyframes flicker {
  0% { transform: scale(1) rotate(-2deg); }
  100% { transform: scale(1.1) rotate(2deg); }
}

@keyframes blowOut {
  0% { opacity: 1; transform: scale(1); }
  100% { opacity: 0; transform: scale(0); }
}

.cake-layer {
  position: absolute;
  border-radius: 10px;
  left: 50%;
  transform: translateX(-50%);
}

.layer1 {
  width: 180px;
  height: 50px;
  background: linear-gradient(to bottom, #ff9a9e, #fecfef);
  bottom: 0;
}

.layer2 {
  width: 140px;
  height: 45px;
  background: linear-gradient(to bottom, #a18cd1, #fbc2eb);
  bottom: 50px;
}

.layer3 {
  width: 100px;
  height: 40px;
  background: linear-gradient(to bottom, #ffecd2, #fcb69f);
  bottom: 95px;
}

.message-container {
  text-align: center;
  color: white;
  z-index: 10;
}

.birthday-title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 20px;
  opacity: 0;
  transform: translateY(-20px);
  transition: all 0.8s ease;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.birthday-title.show {
  opacity: 1;
  transform: translateY(0);
}

.birthday-subtitle {
  font-size: 1.5rem;
  margin-bottom: 30px;
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.8s ease;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
}

.birthday-subtitle.show {
  opacity: 1;
  transform: translateY(0);
}

.blow-btn {
  padding: 15px 40px;
  font-size: 1.2rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: 3px solid white;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.blow-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.wish-text {
  font-size: 1.8rem;
  margin-top: 30px;
  animation: fadeInUp 1s ease;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.confetti {
  position: absolute;
  width: 10px;
  height: 10px;
  animation: fall 3s linear forwards;
}

@keyframes fall {
  0% {
    top: -10px;
    opacity: 1;
  }
  100% {
    top: 110vh;
    opacity: 0;
  }
}
</style>
