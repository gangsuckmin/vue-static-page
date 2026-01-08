<template>
  <main class="page">
    <!-- Background: paper + eternal glow -->
    <div class="bg" aria-hidden="true"></div>
    <div class="grain" aria-hidden="true"></div>
    <div class="glow g1" aria-hidden="true"></div>
    <div class="glow g2" aria-hidden="true"></div>

    <header class="hero" aria-label="결혼기념일 인사">
      <div class="container">
        <p class="eyebrow">Always on the same side</p>
        <p class="eyebrow">1995.01.08</p>
        <h1 class="title">강신재 <span class="heart" aria-hidden="true">♥</span> 임정숙</h1><br>
        <p class="eyebrow">그날의 마음을 간직한 채, 늘 같은 편으로</p>


        <div class="meta" role="group" aria-label="기념일 정보">
          <div class="pill">
            <span class="k">오늘</span>
            <span class="v"><span class="year">{{ todayText }}</span></span>
          </div>
          <div class="pill">
            <span class="k">함께한지</span>
            <span class="v"><span class="year">{{ yearsTogether }}년째</span></span>
          </div>
        </div>
      </div>
    </header>

    <!-- Center video -->
    <section class="video-section" aria-label="기념일 영상">
      <div class="container">
          <div class="video-wrap">
            <video
              class="video"
              :src="currentVideoSrc"
              :poster="posterSrc || undefined"
              controls
              playsinline
              preload="metadata"
              controlsList="nodownload"
              @ended="onEnded"
              ref="videoEl"
            ></video>
          </div>
      </div>
    </section>

    <footer class="footer" aria-label="마무리">
      <p class="thanks">결혼기념일을 축하드립니다</p>
      <small>© {{ new Date().getFullYear() }} Happy Anniversary</small>
    </footer>
  </main>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'

// Vite가 base 경로(/vue-static-page/ 같은)를 쓰는 경우가 있어서 BASE_URL을 붙여줍니다.
// public/anniversary.mp4, public/anniversary.jpg 를 두면 아래 경로로 접근됩니다.
const base = import.meta.env.BASE_URL
const playlist = [`${base}anniversary2.mp4`, `${base}anniversary.mp4`]
const currentIndex = ref(0)
const currentVideoSrc = computed(() => playlist[currentIndex.value] ?? playlist[0])
const videoEl = ref<HTMLVideoElement | null>(null)

function onEnded() {
  // 다음 영상으로 넘어가고, 재생을 이어갑니다.
  currentIndex.value = (currentIndex.value + 1) % playlist.length
  requestAnimationFrame(() => {
    videoEl.value?.play().catch(() => {
      // 모바일 정책상 자동 재생이 막히면 사용자가 재생 버튼을 누르면 됩니다.
    })
  })
}

const posterSrc = `${base}anniversary.jpg`

// ✅ 여기 날짜만 원하는 결혼 날짜로 바꿔주면 'N년째'가 정확해져요.
// 예) 1998-10-11
const WEDDING_DATE = '1995-01-08'

const today = new Date()

const todayText = computed(() => {
  // 모바일에서 보기 좋게: 2026. 01. 08.
  const y = today.getFullYear()
  const m = String(today.getMonth() + 1).padStart(2, '0')
  const d = String(today.getDate()).padStart(2, '0')
  return `${y}. ${m}. ${d}.`
})

const yearsTogether = computed(() => {
  const start = new Date(WEDDING_DATE)
  if (Number.isNaN(start.getTime())) return 0
  let years = today.getFullYear() - start.getFullYear()
  const hasHadAnniversaryThisYear =
    today.getMonth() > start.getMonth() ||
    (today.getMonth() === start.getMonth() && today.getDate() >= start.getDate())
  if (!hasHadAnniversaryThisYear) years -= 1
  // '함께한 0년째' 방지
  return Math.max(1, years + 1)
})
</script>

<style scoped>
/* ===== 모바일 우선: 통일감(색/폰트/간격) + 보기 편한 레이아웃 ===== */
.page {
  position: relative;
  min-height: 100dvh;
  color: var(--ink);
  overflow-x: hidden;
}

.container {
  width: min(560px, 100%);
  margin: 0 auto;
  padding-inline: 1rem;
}

/* Background */
.bg {
  position: fixed;
  inset: 0;
  background:
    radial-gradient(1100px 700px at 18% 12%, rgba(255, 213, 170, .45), transparent 55%),
    radial-gradient(900px 600px at 92% 22%, rgba(190, 210, 255, .50), transparent 55%),
    linear-gradient(180deg, #fff7f2 0%, #ffffff 45%, #f6f9ff 100%);
  z-index: -3;
}

.grain {
  position: fixed;
  inset: -20px;
  z-index: -2;
  opacity: .08;
  background-image:
    repeating-linear-gradient(0deg, rgba(0,0,0,.04) 0, rgba(0,0,0,.04) 1px, transparent 1px, transparent 3px),
    repeating-linear-gradient(90deg, rgba(0,0,0,.03) 0, rgba(0,0,0,.03) 1px, transparent 1px, transparent 4px);
  mix-blend-mode: multiply;
  pointer-events: none;
}

.glow {
  position: fixed;
  width: 54vmin;
  height: 54vmin;
  border-radius: 999px;
  filter: blur(36px);
  opacity: .50;
  z-index: -1;
}
.glow.g1 { top: 10vh; left: -14vmin; background: rgba(255, 162, 132, .45); }
.glow.g2 { bottom: 10vh; right: -16vmin; background: rgba(120, 170, 255, .38); }

/* Hero */
.hero {
  padding: calc(2.1rem + env(safe-area-inset-top)) 0 1.0rem;
  text-align: center;
}

.eyebrow {
  margin: 0 0 .55rem;
  font-size: .78rem;
  letter-spacing: .22em;
  text-transform: uppercase;
  color: rgba(15, 23, 42, .55);
}

.title {
  margin: 0;
  font-size: clamp(2.05rem, 7vw, 3.05rem);
  line-height: 1.08;
  font-weight: 850;
  font-family: ui-serif, Georgia, "Times New Roman", Times, serif;
  color: rgba(15, 23, 42, .92);
}

.heart {
  display: inline-block;
  margin: 0 .25rem;
  font-size: .75em;
  vertical-align: middle;
  color: rgba(255, 120, 150, .75);
}

.subtitle {
  margin: .55rem auto 0;
  max-width: 30ch;
  font-size: 1.02rem;
  line-height: 1.55;
  color: rgba(15, 23, 42, .70);
}

.meta {
  display: flex;
  justify-content: center;
  gap: .55rem;
  flex-wrap: wrap;
  margin-top: .95rem;
}

.pill {
  display: inline-flex;
  align-items: center;
  gap: .45rem;
  padding: .55rem .75rem;
  border-radius: 999px;
  background: rgba(255,255,255,.75);
  border: 1px solid rgba(15, 23, 42, .10);
  box-shadow: 0 10px 22px rgba(15, 23, 42, .08);
  backdrop-filter: blur(6px);
}

.pill .k {
  font-size: .85rem;
  color: rgba(15, 23, 42, .55);
}

.pill .v {
  font-weight: 850;
  color: rgba(15, 23, 42, .82);
}
.pill .year {
  font-size: .9em; /* 숫자/년도만 살짝 작게 */
  font-weight: 800;
}

/* Video section */
.video-section {
  padding: .25rem 0 .7rem;
}

.card {
  background: rgba(255,255,255,.78);
  border: 1px solid rgba(17, 24, 39, .10);
  border-radius: 20px;
  padding: 1.05rem;
  box-shadow: 0 18px 52px rgba(15, 23, 42, .10);
  backdrop-filter: blur(6px);
}

.card-head {
  margin-bottom: .9rem;
  text-align: left;
}

.card-title {
  margin: 0;
  font-size: 1.12rem;
  font-weight: 850;
  color: rgba(15, 23, 42, .90);
}

.card-sub {
  margin: .35rem 0 0;
  font-size: .95rem;
  line-height: 1.45;
  color: rgba(15, 23, 42, .62);
}

.video-wrap {
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid rgba(15, 23, 42, .12);
  box-shadow: 0 12px 22px rgba(15, 23, 42, .12);
  /* 좌우 레터박스(세로영상)용 '하늘+구름' 톤 */
  background:
    radial-gradient(120% 100% at 50% 18%, rgba(235, 247, 255, .98), rgba(255, 255, 255, .96) 46%, rgba(230, 242, 255, .92) 74%),
    linear-gradient(180deg, #eaf4ff 0%, #ffffff 55%, #e6f0ff 100%);
  width: 100%;
  /* 모바일에서 박스 자체를 더 크게(상하로 더 길게) */
  aspect-ratio: 16 / 14;
  max-height: 80vh;
}

.video {
  width: 100%;
  height: 100%;
  display: block;
  object-fit: contain;
}

/* Footer */
.footer {
  padding: .8rem 0 calc(1.35rem + env(safe-area-inset-bottom));
  text-align: center;
  color: rgba(15, 23, 42, .55);
}

.thanks {
  margin: 0;
  font-size: .98rem;
  line-height: 1.5;
  color: rgba(15, 23, 42, .62);
}

.footer small {
  display: block;
  margin-top: .55rem;
}

/* Focus */
:focus-visible {
  outline: 3px solid rgba(120, 170, 255, .85);
  outline-offset: 2px;
  border-radius: 10px;
}

@media (min-width: 900px) {
  .container { width: min(860px, 100%); }
  .hero { padding: 3.0rem 0 1.2rem; }
  .subtitle { max-width: 60ch; }
  .card { padding: 1.2rem; }
  .card-title { font-size: 1.18rem; }
}
</style>

<style>
:root {
  --ink: #0f172a;
  --paper: #ffffff;
}

* { box-sizing: border-box; }

html, body {
  height: 100%;
  margin: 0;
  font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, "Apple SD Gothic Neo", "Noto Sans KR", "Malgun Gothic", Arial, sans-serif;
  background: var(--paper);
  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
}

code { font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace; }
</style>