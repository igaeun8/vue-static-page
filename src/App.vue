<template>
  <div class="page">
    <!-- 별들 -->
    <div class="stars">
      <div v-for="n in 30" :key="n" class="star" :style="getStarStyle(n)"></div>
    </div>
    
    <!-- 1️⃣ 첫 화면 -->
    <main v-if="step === 1" class="main-screen first-screen">
      <!-- 달 + 토끼 -->
      <div class="moon-wrap">
        <div class="moon">
          <div class="rabbit">
            <div class="ear left"></div>
            <div class="ear right"></div>
            <div class="body"></div>
          </div>
        </div>
        <div class="cloud cloud-left"></div>
        <div class="cloud cloud-right"></div>
      </div>

      <!-- 헤더 -->
      <header class="header">
        <h1>🌕 행복한 한가위 🌾</h1>
      </header>

      <p class="lead">마음을 담은 덕담을 골라보세요</p>
      <button class="btn start-btn" @click="step = 2">덕담 고르기</button>
    </main>

    <!-- 2️⃣ 카드 선택 화면 -->
    <main v-if="step === 2" class="main-screen card-screen">
      <div class="grid-2x2">
        <article
          v-for="(card, idx) in cards"
          :key="idx"
          class="card-outer"
          @click="selectCard(card)"
        >
          <div class="card">
            <div class="card-face card-front">
              <div class="card-content">
                <div class="mini-moon"></div>
                <div class="front-text">덕담 보기</div>
              </div>
            </div>
          </div>
        </article>
      </div>
    </main>

    <!-- 3️⃣ 결과 화면 -->
    <main v-if="step === 3" class="main-screen result-screen">
      <div class="result-card">
        <p class="blessing big">{{ selectedCard.message }}</p>
        <div class="result-actions">
          <button class="btn retry" @click="reset">다시 고르기</button>
          <button class="btn share" @click="share(selectedCard.message)">공유하기</button>
        </div>
      </div>
    </main>

    <footer class="footer">✨ 달빛과 덕담이 함께하는 한가위 ✨</footer>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

const step = ref(1);
const selectedCard = ref<any>(null);

// 별 위치 랜덤 생성 (n을 seed로 사용하여 재현 가능하게)
function getStarStyle(n: number) {
  const seed = n * 17;
  const left = (Math.sin(seed) * 50 + 50) % 100;
  const top = (Math.cos(seed) * 50 + 50) % 100;
  const delay = (seed % 300) / 100;
  const duration = 1 + (seed % 200) / 100;
  return {
    left: `${left}%`,
    top: `${top}%`,
    animationDelay: `${delay}s`,
    animationDuration: `${duration}s`,
  };
}

const cards = [
  { message: "🌕 풍성한 한가위 되세요!" },
  { message: "🍂 가족과 함께 따뜻한 시간 보내세요." },
  { message: "✨ 보름달처럼 소원이 이루어지길!" },
  { message: "🥮 맛있는 송편처럼 행복이 가득하길!" },
];

function selectCard(card: any) {
  selectedCard.value = card;
  step.value = 3;
}

function reset() {
  selectedCard.value = null;
  step.value = 2;
}

function share(text: string) {
  const payload = `${text} — 한가위 덕담`;
  if (navigator.share) {
    navigator.share({ title: "한가위 덕담", text: payload, url: window.location.href })
      .catch(() => {
        navigator.clipboard?.writeText(`${payload}\n${window.location.href}`);
        alert("공유를 취소했거나 지원하지 않는 환경입니다. 텍스트를 복사했습니다.");
      });
  } else if (navigator.clipboard) {
    navigator.clipboard.writeText(`${payload}\n${window.location.href}`);
    alert("텍스트가 클립보드에 복사되었습니다.");
  } else {
    alert("이 브라우저에서는 자동 공유를 지원하지 않습니다.");
  }
}
</script>

<style scoped>
/* 페이지 전체 */
.page {
  min-height: 100vh;
  min-width: 60vw;
  display: flex;
  flex-direction: column;
  align-items: center;
  background: radial-gradient(ellipse 300% 120% at 10% 10%, #10131f 0%, #0b0620 60%, #03020a 100%);
  color: #fff;
  font-family: 'Noto Sans KR', 'Pretendard', system-ui, sans-serif;
  padding: 36px 20px 60px;
  box-sizing: border-box;
  position: relative;
  overflow: hidden;
}

/* 별들 */
.stars {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.star {
  position: absolute;
  width: 2px;
  height: 2px;
  background: white;
  border-radius: 50%;
  box-shadow: 0 0 4px rgba(255, 255, 255, 0.8);
  animation: twinkle linear infinite;
}

@keyframes twinkle {
  0%, 100% {
    opacity: 0.2;
    transform: scale(1);
  }
  50% {
    opacity: 1;
    transform: scale(1.5);
  }
}

/* 달 + 토끼 */
.moon-wrap { 
  position: relative; 
  z-index: 1; 
  margin-bottom: 32px;
}
.moon {
  width: 200px; height: 200px; border-radius: 50%;
  background: radial-gradient(circle at 30% 30%, #fff7cf, #ffd45a 60%, #f7c53a 100%);
  box-shadow: 0 12px 60px rgba(255, 200, 70, 0.18), 0 2px 8px rgba(0,0,0,0.5);
  display: flex; align-items: center; justify-content: center;
}
.rabbit .ear { position: absolute; width: 22px; height: 48px; background: white; border-radius: 14px; top: 36px; }
.rabbit .ear.left { left: 72px; transform: rotate(-18deg); }
.rabbit .ear.right { left: 100px; transform: rotate(18deg); }
.rabbit .body { position: absolute; width: 68px; height: 54px; background: white; border-radius: 50%; top: 76px; left: 78px; transform: rotate(12deg); }
.cloud { position: absolute; background: rgba(255,255,255,0.06); border-radius: 50%; filter: blur(6px); }
.cloud-left { width: 120px; height: 36px; top: 140px; left: calc(50% - 260px); }
.cloud-right { width: 150px; height: 44px; top: 150px; right: calc(50% - 260px); }

/* 헤더 */
.header { text-align: center; z-index: 1; position: relative; margin-bottom: 32px; }
.header h1 { font-size: 1.8rem; margin: 0; }
.lead { 
  font-size: 1rem; 
  color: rgba(255,245,200,0.7);
  margin-bottom: 32px;
  text-align: center;
}

/* 공통 화면 */
.main-screen {
  width: 100%;
  max-width: 980px;
  min-height: 480px; /* 첫, 카드 선택, 결과 화면 동일 */
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  position: relative;
  z-index: 1;
}

/* 첫 화면 전용 스타일 */
.first-screen {
  justify-content: flex-start;
  padding-top: 80px;
}

/* 카드 선택 화면 스타일 */
.card-screen {
  justify-content: flex-start;
  align-items: center;
  padding-top: 120px;
}

/* 결과 화면 스타일 */
.result-screen {
  justify-content: flex-start;
  padding-top: 120px;
}

/* 첫 화면 버튼 */
.start-btn {
  padding: 16px 32px;
  font-size: 1.2rem;
  border-radius: 18px;
  border: 1px solid rgba(255,255,255,0.2);
  background: rgba(255,255,255,0.05);
  color: #fff;
  cursor: pointer;
  transition: all 0.3s ease;
}
.start-btn:hover {
  background: rgba(255,255,255,0.15);
}

/* 카드 선택 화면 */
.grid-2x2 {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 100px 20px;
  width: 100%;
  max-width: 500px;
  justify-items: center;
  padding: 20px;
  box-sizing: border-box;
  margin: 0 auto;
  justify-content: space-between;
}
.card-outer {
  width: 120px;
  height: 140px;
  perspective: 1200px;
  cursor: pointer;
  position: relative;
}
.card {
  width: 100%;
  height: 100%;
  border-radius: 18px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8px 30px rgba(2,8,23,0.6), 0 0 20px rgba(255, 255, 255, 0.1);
  transition: transform 0.7s cubic-bezier(.2,.9,.3,1), background 0.3s ease;
  cursor: pointer;
}

.card:hover {
  background: rgba(255, 255, 255, 0.2);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 18px;
  backface-visibility: hidden;
}

.card-front {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 8px 30px rgba(2,8,23,0.6), 0 0 20px rgba(255, 255, 255, 0.1);
}

.card-back {
  background: rgba(254, 249, 195, 0.85);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  transform: rotateY(180deg);
  box-shadow: 0 8px 30px rgba(2,8,23,0.6), 0 0 20px rgba(255, 255, 255, 0.1);
}

.card-face .card-content {
  display: flex; 
  flex-direction: column; 
  align-items: center; 
  gap: 12px;
  width: 100%;
  height: 100%;
  justify-content: center;
}
.mini-moon {
  width: 80px; height: 80px;
  border-radius: 50%;
  background: radial-gradient(circle at 28% 28%, #fffbe6, #f9d96a 60%);
  box-shadow: 0 6px 30px rgba(255, 200, 60, 0.15), inset 0 -6px 16px rgba(0,0,0,0.12);
}
.front-text {
  font-weight: 700;
  color: #fffad0;
}

/* 결과 화면 */
.result-card {
  width: 240px;
  height: 220px;
  border-radius: 18px;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.2);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 16px;
  box-shadow: 0 12px 40px rgba(0,0,0,0.6), 0 0 20px rgba(255, 255, 255, 0.1);
  transition: background 0.3s ease;
}

.result-card:hover {
  background: rgba(255, 255, 255, 0.2);
}
.blessing.big { font-size: 1.25rem; font-weight: 800; text-align: center; }
.result-actions { display: flex; gap: 12px; }
.btn { border: 1px solid rgba(255,255,255,0.08); background: rgba(255,255,255,0.04); color: #fff; padding: 10px 14px; border-radius: 999px; font-weight: 700; cursor: pointer; transition: all 0.2s ease; }
.btn:hover { background: rgba(255,255,255,0.12); }

/* footer */
.footer { 
  margin-top: 40px; 
  opacity: 0.6; 
  font-size: 0.92rem; 
  text-align: center; 
  position: relative;
  z-index: 1;
}

/* 반응형 */
@media (max-width: 880px) {
  .grid-2x2 { grid-template-columns: repeat(2, 1fr); gap: 20px; max-width: 360px; }
  .card-outer, .result-card { width: 42vw; height: 200px; max-width: 160px; }
}
@media (max-width: 520px) {
  .grid-2x2 { grid-template-columns: 1fr; gap: 16px; max-width: 280px; }
  .card-outer, .result-card { width: 80vw; height: 180px; max-width: 200px; }
}
</style>
