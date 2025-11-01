<template>
  <main class="page">
    <!-- 배경 장식 -->
    <div class="bg-gradient" aria-hidden="true"></div>
    <div class="moon" aria-hidden="true"></div>
    <div class="cloud c1" aria-hidden="true"></div>
    <div class="cloud c2" aria-hidden="true"></div>

    <!-- 헤더 -->
    <header class="header">
      <div class="taegeuk" aria-hidden="true">
        <span class="half red"></span>
        <span class="half blue"></span>
      </div>
      <h1 class="title">풍요로운 한가위</h1>
      <p class="subtitle">붉은 기쁨, 푸른 평안이 가득하길</p>
    </header>

    <!-- 컨텐츠 -->
    <section class="cards">
      <article class="card">
        <h2 class="card-title">송편 한 접시</h2>
        <p class="card-text">
          둥근 달처럼 마음도 둥글게—가족과 나누는 정이 가장 큰 선물이지요.
        </p>
        <div class="ricecakes" aria-hidden="true">
          <span class="rc rc-red"></span>
          <span class="rc rc-blue"></span>
          <span class="rc rc-red"></span>
          <span class="rc rc-blue"></span>
        </div>
      </article>

      <article class="card">
        <h2 class="card-title">붉고 푸른 등(燈)</h2>
        <p class="card-text">
          희망을 담은 연등이 밤하늘을 수놓습니다. 살짝 흔들리는 불빛을 감상해 보세요.
        </p>
        <div class="lanterns" aria-hidden="true">
          <div class="lantern lantern-red">
            <div class="string"></div><div class="body"></div><div class="tassel"></div>
          </div>
          <div class="lantern lantern-blue">
            <div class="string"></div><div class="body"></div><div class="tassel"></div>
          </div>
          <div class="lantern lantern-red">
            <div class="string"></div><div class="body"></div><div class="tassel"></div>
          </div>
        </div>
      </article>

      <article class="card wish-card">
        <h2 class="card-title">소원 빌기</h2>
        <form class="wish-form" @submit.prevent="addWish">
          <input
              v-model.trim="wishInput"
              class="wish-input"
              type="text"
              placeholder="보름달에게 빌 소원을 적어 보세요"
              maxlength="60"
              aria-label="소원 입력"
          />
          <button class="wish-btn" type="submit" :disabled="!wishInput">빌기</button>
        </form>

        <ul class="wish-list" v-if="wishes.length">
          <li v-for="(w, i) in wishes" :key="w.id" class="wish-item">
            <span class="dot" :class="i % 2 ? 'blue' : 'red'"></span>
            <span class="wish-text">{{ w.text }}</span>
            <button class="remove" @click="removeWish(w.id)" aria-label="소원 삭제">×</button>
          </li>
        </ul>
        <p v-else class="empty">첫 소원을 적어보세요. 🌕</p>
      </article>
    </section>

    <!-- 푸터 -->
    <footer class="footer">
      <small>© {{ year }} Chuseok with <span class="heart">❤</span> Red & Blue</small>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { onMounted, ref, watch } from 'vue'

type Wish = { id: string; text: string }
const wishInput = ref('')
const wishes = ref<Wish[]>([])
const STORAGE_KEY = 'chuseok-wishes'
const year = new Date().getFullYear()

onMounted(() => {
  try {
    const raw = localStorage.getItem(STORAGE_KEY)
    if (raw) wishes.value = JSON.parse(raw) as Wish[]
  } catch { /* ignore */ }
})

watch(wishes, (v) => {
  try {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(v))
  } catch { /* ignore */ }
}, { deep: true })

function addWish() {
  if (!wishInput.value) return
  wishes.value.unshift({ id: crypto.randomUUID(), text: wishInput.value })
  wishInput.value = ''
}

function removeWish(id: string) {
  wishes.value = wishes.value.filter(w => w.id !== id)
}
</script>

<style scoped>
/* ===== 색상 토큰 ===== */
:root {
  --red-500: #e11d48;   /* 메인 레드 */
  --red-600: #be123c;
  --blue-500: #2563eb;  /* 메인 블루 */
  --blue-600: #1d4ed8;
  --ink: #0b1020;
  --paper: #f8fafc;
  --muted: #e5e7eb;
  --shadow: rgba(0,0,0,.25);
}

/* ===== 레이아웃 ===== */
.page {
  position: relative;
  min-height: 100dvh;
  color: var(--paper);
  overflow-x: hidden;
  display: grid;
  grid-template-rows: auto 1fr auto;
}

/* 배경: 레드→블루 그라디언트 */
.bg-gradient {
  position: fixed;
  inset: 0;
  background: radial-gradient(80vw 80vh at 80% 10%, var(--blue-600), transparent 60%),
  radial-gradient(80vw 80vh at 20% 90%, var(--red-600), transparent 60%),
  linear-gradient(160deg, var(--red-500), var(--blue-500));
  z-index: -3;
}

/* 보름달 */
.moon {
  position: fixed;
  top: 8vh; right: 10vw;
  width: min(22rem, 35vw);
  aspect-ratio: 1/1;
  border-radius: 50%;
  background:
      radial-gradient(circle at 60% 40%, rgba(255,255,255,.9), rgba(255,255,255,.75) 40%, rgba(255,255,255,.0) 70%),
      #ffffff;
  box-shadow:
      0 0 60px 20px rgba(255,255,255,.35),
      0 0 120px 40px rgba(37, 99, 235, .25);
  animation: moonrise 1.8s ease-out both;
  z-index: -2;
}
@keyframes moonrise {
  from { transform: translateY(30px); opacity: .0; }
  to   { transform: translateY(0); opacity: 1; }
}

/* 구름 */
.cloud {
  position: fixed;
  height: 16vmin;
  width: 40vmin;
  filter: blur(10px);
  background: linear-gradient(90deg, rgba(255,255,255,.12), rgba(255,255,255,.04));
  border-radius: 999px;
  z-index: -1;
  opacity: .7;
}
.c1 { top: 22vh; left: -20vmin; animation: drift 30s linear infinite; }
.c2 { top: 55vh; right: -25vmin; animation: drift 40s linear infinite reverse; }
@keyframes drift {
  from { transform: translateX(0); }
  to   { transform: translateX(120vmin); }
}

/* 헤더 */
.header {
  text-align: center;
  padding: 6rem 1.25rem 2rem;
}
.title {
  margin: .75rem 0 0;
  font-size: clamp(2rem, 5vw, 3rem);
  font-weight: 800;
  letter-spacing: .02em;
  text-shadow: 0 6px 16px var(--shadow);
}
.subtitle {
  opacity: .95;
  margin-top: .25rem;
}

/* 태극 포인트 */
.taegeuk {
  display: inline-grid;
  grid-template-columns: 1fr 1fr;
  width: clamp(64px, 14vw, 96px);
  aspect-ratio: 1/1;
  border-radius: 999px;
  overflow: hidden;
  box-shadow: 0 8px 24px var(--shadow);
}
.half { display: block; }
.half.red  { background: radial-gradient(circle at 70% 30%, #ff7aa2, var(--red-500)); }
.half.blue { background: radial-gradient(circle at 30% 70%, #7aa7ff, var(--blue-500)); }

/* 카드 그리드 */
.cards {
  display: grid;
  gap: 1.25rem;
  grid-template-columns: repeat(12, 1fr);
  padding: 1rem clamp(1rem, 4vw, 2rem) 3rem;
}
.card {
  grid-column: span 12;
  background: rgba(255,255,255,.08);
  border: 1px solid rgba(255,255,255,.18);
  border-radius: 16px;
  padding: 1rem;
  backdrop-filter: blur(6px);
  box-shadow: 0 10px 30px rgba(0,0,0,.25);
}
.card-title {
  font-weight: 700;
  margin: .25rem 0 .5rem;
  font-size: 1.2rem;
}
.card-text { opacity: .95; }

@media (min-width: 900px) {
  .card:nth-child(1) { grid-column: span 4; }
  .card:nth-child(2) { grid-column: span 4; }
  .wish-card       { grid-column: span 4; }
}

/* 송편 데코 */
.ricecakes {
  display: flex; gap: .5rem; margin-top: .75rem;
}
.rc {
  width: 36px; height: 24px; border-radius: 18px 18px 6px 6px;
  box-shadow: inset 0 -3px 0 rgba(0,0,0,.12), 0 6px 12px rgba(0,0,0,.2);
}
.rc-red  { background: linear-gradient(#ff89a8, var(--red-500)); }
.rc-blue { background: linear-gradient(#8fb0ff, var(--blue-500)); }

/* 연등 애니메이션 */
.lanterns { display: flex; gap: 1rem; margin-top: .75rem; }
.lantern { display: grid; place-items: center; width: 70px; }
.lantern .string { width: 2px; height: 18px; background: rgba(255,255,255,.7); }
.lantern .body {
  width: 54px; height: 64px; border-radius: 12px;
  border: 2px solid rgba(255,255,255,.65);
  box-shadow: inset 0 0 18px rgba(255,255,255,.6), 0 10px 18px rgba(0,0,0,.25);
  animation: sway 3.6s ease-in-out infinite;
}
.lantern .tassel { width: 6px; height: 18px; background: rgba(255,255,255,.8); margin-top: 2px; border-radius: 0 0 3px 3px; }
.lantern-red .body  { background: linear-gradient(180deg, #ffb3c4, var(--red-500)); }
.lantern-blue .body { background: linear-gradient(180deg, #b7ccff, var(--blue-500)); }
.lantern:nth-child(2) .body { animation-delay: .6s; }
.lantern:nth-child(3) .body { animation-delay: 1.2s; }
@keyframes sway {
  0%, 100% { transform: rotate(-4deg) translateY(0); }
  50%      { transform: rotate(4deg) translateY(2px); }
}

/* 소원 폼 */
.wish-form { display: flex; gap: .5rem; margin-top: .5rem; }
.wish-input {
  flex: 1;
  padding: .7rem .9rem;
  border-radius: 10px;
  border: 1px solid rgba(255,255,255,.35);
  background: rgba(255,255,255,.15);
  color: #fff;
  outline: none;
}
.wish-input::placeholder { color: rgba(255,255,255,.75); }
.wish-btn {
  padding: .7rem 1rem;
  border-radius: 10px;
  border: none;
  font-weight: 700;
  background: linear-gradient(135deg, var(--red-500), var(--blue-500));
  color: white;
  box-shadow: 0 8px 16px rgba(0,0,0,.25);
  cursor: pointer;
}
.wish-btn:disabled { opacity: .6; cursor: not-allowed; }

.wish-list { list-style: none; margin: .75rem 0 0; padding: 0; display: grid; gap: .5rem; }
.wish-item {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: .5rem;
  padding: .6rem .7rem;
  border-radius: 10px;
  background: rgba(255,255,255,.1);
  border: 1px solid rgba(255,255,255,.18);
}
.dot {
  width: .75rem; height: .75rem; border-radius: 999px; display: inline-block;
  box-shadow: 0 0 0 3px rgba(255,255,255,.15);
}
.dot.red  { background: var(--red-500); }
.dot.blue { background: var(--blue-500); }
.wish-text { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.remove {
  background: transparent; border: none; color: #fff; font-size: 1.1rem; cursor: pointer;
  line-height: 1; padding: .25rem;
}
.empty { opacity: .9; margin-top: .75rem; }

/* 푸터 */
.footer {
  text-align: center;
  padding: 1.5rem 1rem 2.5rem;
  color: rgba(255,255,255,.9);
}
.heart { color: #fff; text-shadow: 0 0 10px rgba(255,255,255,.6); }

/* 접근성 */
:focus-visible { outline: 3px solid #fff; outline-offset: 2px; border-radius: 8px; }
</style>