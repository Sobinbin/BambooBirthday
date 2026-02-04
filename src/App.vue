<template>
  <div class="birthday-app" @click="handleClick">
    <!-- 开场页面 -->
    <div v-if="stage === 'opening'" class="stage opening">
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
      
      <div class="opening-text">
        <h1 class="title" :class="{ 'show': showTitle }">
          Bamboo，生日快乐
        </h1>
        <p class="subtitle" :class="{ 'show': showSubtitle }">
          总有些惊奇的际遇，比方说当我遇见你
        </p>
        <button @click.stop="startJourney" class="start-btn" :class="{ 'show': showBtn }">
          开始吧！
        </button>
      </div>
      
      <div class="floating-hearts">
        <div v-for="(heart, index) in hearts" :key="index" class="heart" :style="heart.style">
          ❤️
        </div>
      </div>
    </div>

    <!-- 图片祝福展示 -->
    <div v-else-if="stage === 'showcase'" class="stage showcase">
      <transition name="slide" mode="out-in">
        <div :key="currentIndex" class="showcase-content">
          <div class="image-container">
            <img :src="currentSlide.image" :alt="'生日回忆 ' + (currentIndex + 1)" class="slide-image">
            <div class="image-frame"></div>
          </div>
          
          <div class="text-container">
            <div class="slide-number">{{ currentIndex + 1 }} / {{ slides.length }}</div>
            <h2 class="slide-title">{{ currentSlide.title }}</h2>
            <p class="slide-message">{{ currentSlide.message }}</p>
            <div class="hint">点击或等待继续</div>
          </div>
        </div>
      </transition>
      
      <div class="progress-dots">
        <div 
          v-for="(slide, index) in slides" 
          :key="index"
          class="dot"
          :class="{ 'active': index === currentIndex, 'completed': index < currentIndex }"
          @click.stop="goToSlide(index)"
        ></div>
      </div>
    </div>

    <!-- 结束页面 -->
    <div v-else-if="stage === 'ending'" class="stage ending">
      <div class="ending-content">
        <h1 class="ending-title">感谢你出现在我的生命里</h1>
        <div class="ending-message">
          <p v-for="(msg, index) in endingMessages" :key="index" :class="{ 'show': showEnding[index] }">
            {{ msg }}
          </p>
        </div>
        <button @click.stop="restart" class="restart-btn" :class="{ 'show': showRestartBtn }">
          🎂 再看一遍
        </button>
      </div>
      
      <div class="confetti-container">
        <div v-for="(confetti, index) in confettiPieces" :key="index" class="confetti" :style="confetti.style">
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const stage = ref('opening')
const showTitle = ref(false)
const showSubtitle = ref(false)
const showBtn = ref(false)
const isBlowing = ref(false)
const currentIndex = ref(0)
const hearts = ref([])
const confettiPieces = ref([])
const showEnding = ref([false, false, false, false])
const showRestartBtn = ref(false)
const autoPlayTimer = ref(null)

const slides = [
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/11_29.jpg',
    title: '11.29-路演计划',
    message: '还记得第一次见面的场景吗，如果下次被选中，要一起唱哪首歌呢？天冷的时候记得穿秋裤哦！'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/12_14.jpg',
    title: '12.14-坦白局',
    message: '坏了，好像喜欢上和你一块唱歌🎤喝酒🍷了。要保守我们之间的秘密哦🤫。'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/12_21.jpg',
    title: '12.21-崇礼',
    message: '心跳💗加速是因为滑雪🎿还是因为你呢，有些分不清了。谢谢你教会了我，在哪摔倒，就在哪拍照。'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_11.jpg',
    title: '1.11-花束般的恋爱',
    message: '看到了你脆弱柔软的一面，对于养过猫的人，我知道，猫咪翻肚皮意味着她很信任你。'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_16.jpg',
    title: '1.16-巅峰对决',
    message: '别再刀我了'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_18.jpg',
    title: '1.18-颐和园',
    message: '喜欢你笑起来的样子。你答应我了还要一起去好多好多地方，故宫、景山、北海公园......'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_25.jpg',
    title: '1.25-坏女人',
    message: '🐱嘤嘤嘤，别再rollback了！'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_29.jpg',
    title: '1.29-❤️',
    message: '我喜欢你'
  },
  {
    image: 'https://raw.githubusercontent.com/Sobinbin/BambooBirthday/resource/1_31.jpg',
    title: '1.31-Page One',
    message: '这位同志，请不要停止骚扰！'
  },
]

const endingMessages = [
  '不管未来会怎么样',
  '至少我们现在很开心',
  '不管结局会怎么样',
  '至少想念的人是你',
  '我不会把他当作游戏',
  '因为我真心对你',
  '生日快乐，倩叶'
]

const currentSlide = computed(() => slides[currentIndex.value])

onMounted(() => {
  setTimeout(() => showTitle.value = true, 300)
  setTimeout(() => showSubtitle.value = true, 600)
  setTimeout(() => showBtn.value = true, 900)
  createFloatingHearts()
})

const createFloatingHearts = () => {
  for (let i = 0; i < 15; i++) {
    hearts.value.push({
      style: {
        left: Math.random() * 100 + '%',
        top: Math.random() * 100 + '%',
        animationDelay: Math.random() * 5 + 's',
        fontSize: (Math.random() * 20 + 20) + 'px'
      }
    })
  }
}

const startJourney = () => {
  isBlowing.value = true
  setTimeout(() => {
    stage.value = 'showcase'
    startAutoPlay()
  }, 800)
}

const startAutoPlay = () => {
  clearTimeout(autoPlayTimer.value)
  autoPlayTimer.value = setTimeout(() => {
    nextSlide()
  }, 10000)
}

const handleClick = () => {
  if (stage.value === 'showcase') {
    clearTimeout(autoPlayTimer.value)
    nextSlide()
  }
}

const nextSlide = () => {
  if (currentIndex.value < slides.length - 1) {
    currentIndex.value++
    startAutoPlay()
  } else {
    setTimeout(() => {
      showEndingStage()
    }, 500)
  }
}

const goToSlide = (index) => {
  currentIndex.value = index
  startAutoPlay()
}

const showEndingStage = () => {
  stage.value = 'ending'
  endingMessages.forEach((_, index) => {
    setTimeout(() => {
      showEnding.value[index] = true
    }, (index + 1) * 600)
  })
  setTimeout(() => {
    showRestartBtn.value = true
    createConfetti()
  }, 3000)
}

const createConfetti = () => {
  const colors = ['#ff6b6b', '#4ecdc4', '#45b7d1', '#f9ca24', '#6c5ce7', '#fd79a8', '#ff9ff3', '#54a0ff']
  for (let i = 0; i < 80; i++) {
    confettiPieces.value.push({
      style: {
        left: Math.random() * 100 + '%',
        top: '-20px',
        backgroundColor: colors[Math.floor(Math.random() * colors.length)],
        animationDelay: Math.random() * 3 + 's',
        transform: `rotate(${Math.random() * 360}deg)`,
        width: (Math.random() * 10 + 8) + 'px',
        height: (Math.random() * 10 + 8) + 'px',
        borderRadius: Math.random() > 0.5 ? '50%' : '0'
      }
    })
  }
}

const restart = () => {
  stage.value = 'opening'
  currentIndex.value = 0
  isBlowing.value = false
  showEnding.value = [false, false, false, false]
  showRestartBtn.value = false
  confettiPieces.value = []
  setTimeout(() => showTitle.value = true, 300)
  setTimeout(() => showSubtitle.value = true, 600)
  setTimeout(() => showBtn.value = true, 900)
}
</script>

<style scoped>
.birthday-app {
  width: 100%;
  min-height: 100vh;
  font-family: 'Microsoft YaHei', 'PingFang SC', 'Helvetica Neue', sans-serif;
  overflow: hidden;
}

/* 通用样式 */
.stage {
  width: 100%;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
}

/* 开场页面 */
.opening {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 50%, #ff9a9e 100%);
  padding: 20px;
}

.cake-container {
  margin-bottom: 40px;
  animation: float 3s ease-in-out infinite;
}

@keyframes float {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
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

.opening-text {
  text-align: center;
  color: #fff;
  z-index: 10;
}

.title {
  font-size: 3.5rem;
  font-weight: bold;
  margin-bottom: 15px;
  opacity: 0;
  transform: translateY(-30px);
  transition: all 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
}

.title.show {
  opacity: 1;
  transform: translateY(0);
}

.subtitle {
  font-size: 1.3rem;
  margin-bottom: 40px;
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.15);
}

.subtitle.show {
  opacity: 1;
  transform: translateY(0);
}

.start-btn {
  padding: 18px 50px;
  font-size: 1.4rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
  opacity: 0;
  transform: scale(0.8);
}

.start-btn.show {
  opacity: 1;
  transform: scale(1);
}

.start-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.floating-hearts {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  overflow: hidden;
}

.heart {
  position: absolute;
  animation: floatHeart 6s ease-in-out infinite;
  opacity: 0.6;
}

@keyframes floatHeart {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-20px) rotate(10deg); }
}

/* 图片展示页面 */
.showcase {
  background: linear-gradient(135deg, #a18cd1 0%, #fbc2eb 100%);
  padding: 20px;
  cursor: pointer;
}

.showcase-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 800px;
  width: 100%;
}

.image-container {
  position: relative;
  width: 100%;
  max-width: 500px;
  margin-bottom: 30px;
}

.slide-image {
  width: 100%;
  height: auto;
  border-radius: 20px;
  box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
  display: block;
}

.image-frame {
  position: absolute;
  top: -10px;
  left: -10px;
  right: -10px;
  bottom: -10px;
  border: 4px solid rgba(255, 255, 255, 0.5);
  border-radius: 25px;
  pointer-events: none;
}

.text-container {
  text-align: center;
  color: white;
  padding: 20px;
  background: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(10px);
  border-radius: 20px;
  width: 100%;
  max-width: 600px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
}

.slide-number {
  font-size: 1rem;
  opacity: 0.8;
  margin-bottom: 15px;
  letter-spacing: 2px;
}

.slide-title {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 20px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.slide-message {
  font-size: 1.2rem;
  line-height: 1.8;
  margin-bottom: 15px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.1);
}

.hint {
  font-size: 0.9rem;
  opacity: 0.7;
  margin-top: 15px;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 0.9; }
}

.progress-dots {
  display: flex;
  gap: 10px;
  margin-top: 30px;
}

.dot {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.3);
  transition: all 0.3s ease;
  cursor: pointer;
}

.dot:hover {
  background: rgba(255, 255, 255, 0.8);
  transform: scale(1.2);
}

.dot.active {
  width: 35px;
  border-radius: 6px;
  background: white;
}

.dot.completed {
  background: rgba(255, 255, 255, 0.7);
}

/* 过渡动画 */
.slide-enter-active,
.slide-leave-active {
  transition: all 0.6s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.slide-enter-from {
  opacity: 0;
  transform: translateX(100px) scale(0.9);
}

.slide-leave-to {
  opacity: 0;
  transform: translateX(-100px) scale(0.9);
}

/* 结束页面 */
.ending {
  background: linear-gradient(135deg, #ffecd2 0%, #fcb69f 50%, #ff9a9e 100%);
  padding: 20px;
}

.ending-content {
  text-align: center;
  color: white;
  z-index: 10;
  max-width: 600px;
  padding: 40px 20px;
}

.ending-title {
  font-size: 2.8rem;
  font-weight: bold;
  margin-bottom: 50px;
  text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.2);
  animation: bounceIn 1s ease;
}

@keyframes bounceIn {
  0% { opacity: 0; transform: scale(0.3); }
  50% { transform: scale(1.05); }
  70% { transform: scale(0.9); }
  100% { opacity: 1; transform: scale(1); }
}

.ending-message p {
  font-size: 1.5rem;
  margin-bottom: 25px;
  opacity: 0;
  transform: translateY(20px);
  transition: all 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.ending-message p.show {
  opacity: 1;
  transform: translateY(0);
}

.restart-btn {
  margin-top: 40px;
  padding: 18px 50px;
  font-size: 1.4rem;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
  opacity: 0;
  transform: scale(0.8);
}

.restart-btn.show {
  opacity: 1;
  transform: scale(1);
}

.restart-btn:hover {
  transform: scale(1.05);
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

.confetti-container {
  position: absolute;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
  pointer-events: none;
  overflow: hidden;
}

.confetti {
  position: absolute;
  animation: confettiFall 4s linear forwards;
}

@keyframes confettiFall {
  0% {
    top: -20px;
    opacity: 1;
    transform: rotate(0deg);
  }
  100% {
    top: 110vh;
    opacity: 0;
    transform: rotate(720deg);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .title, .ending-title {
    font-size: 2.5rem;
  }
  
  .subtitle {
    font-size: 1.1rem;
  }
  
  .start-btn, .restart-btn {
    padding: 15px 40px;
    font-size: 1.2rem;
  }
  
  .slide-title {
    font-size: 1.5rem;
  }
  
  .slide-message {
    font-size: 1rem;
  }
  
  .cake {
    width: 160px;
    height: 144px;
  }
  
  .candle:nth-child(1) { left: 32px; }
  .candle:nth-child(2) { left: 56px; }
  .candle:nth-child(3) { left: 77px; }
  .candle:nth-child(4) { left: 98px; }
  .candle:nth-child(5) { left: 122px; }
  
  .layer1 { width: 144px; height: 40px; }
  .layer2 { width: 112px; height: 36px; }
  .layer3 { width: 80px; height: 32px; }
}
</style>