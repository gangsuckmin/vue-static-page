<template>
  <main class="page">
    <h1 class="title">🌕 추석 잘 보내세요!</h1>
    <p class="subtitle">붉은 기쁨과 푸른 평안이 함께 하시길 바랍니다.</p>

    <div class="lantern red"></div>
    <div class="lantern blue"></div>
  </main>
</template>

<script setup lang="ts">
// 간단한 표시용 Vue 컴포넌트, 추가 JS 필요 없음
</script>

<style scoped>
.page {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  background: radial-gradient(circle at 50% 20%, #1b2450, #0b1020);
  color: #fff;
  overflow: hidden;
}
.title {
  font-size: 2.4rem;
  margin-bottom: 0.5rem;
  color: #ffd966;
}
.subtitle {
  font-size: 1.2rem;
  opacity: 0.9;
}

.lantern {
  position: absolute;
  bottom: -60px;
  width: 40px;
  height: 60px;
  border-radius: 8px;
  animation: float 8s ease-in-out infinite;
}
.lantern.red {
  background: linear-gradient(180deg, #ff4b4b, #a60000);
  left: 30%;
  animation-delay: 0s;
}
.lantern.blue {
  background: linear-gradient(180deg, #3b82f6, #1e40ff);
  left: 65%;
  animation-delay: 2s;
}

@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  10% { opacity: 1; }
  50% { transform: translateY(-60vh); }
  100% { transform: translateY(-120vh); opacity: 0; }
}
</style>
