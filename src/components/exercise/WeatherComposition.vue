<script setup>
import { ref, computed, watch, watchEffect } from 'vue'

// 1. [1일차 데이터] 가상의 백엔드 데이터 배열
const weatherList = ref([
  { id: 'city_01', name: '서울', temp: 28, status: '맑음' },
  { id: 'city_02', name: '수원', temp: 24, status: '비' },
  { id: 'city_03', name: '부산', temp: 26, status: '구름' },
])

// 2. [1일차 데이터] 검색어 및 알림창 제어용 데이터
const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

// 3. [2일차 추가] computed를 활용한 실시간 검색 필터링 연산기 (★핵심)
const filteredWeatherList = computed(() => {
  // 사용자가 입력한 검색어의 앞뒤 공백을 제거합니다.
  const query = searchQuery.value.trim()

  // 검색어가 비어있다면 원본 weatherList를 그대로 보여줍니다.
  if (!query) {
    return weatherList.value
  }

  // 검색어가 포함된 도시만 칼같이 필터링하여 실시간으로 뱉어냅니다.
  return weatherList.value.filter((item) => item.name.includes(query))
})

// 4. [2일차 추가] watch를 활용한 선택 도시 추적 센서
// selectedCityInfo의 문구 변화를 감시하여 후속 로그를 처리합니다.
watch(selectedCityInfo, (newInfo) => {
  console.log(`👁️‍🗨️ [watch 감지] 상태 바 문구가 업데이트되었습니다 -> "${newInfo}"`)
})

// 5. [2일차 추가] watchEffect를 활용한 자동 의존성 API 로그 시뮬레이션
// 타이핑할 때마다 변하는 searchQuery를 AI CCTV처럼 자동 추적합니다.
watchEffect(() => {
  console.log(`🤖 [watchEffect 자동 호출] 현재 검색어 '${searchQuery.value}'에 매칭되는 API 데이터를 필터링합니다.`)
})

// 알림 대행 함수
const showDetail = (cityName, status) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태입니다.`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <section class="search-box">
      <h3>🔍 도시 검색</h3>
      <input type="text" :value="searchQuery" @input="(e) => (searchQuery = e.target.value)" placeholder="검색할 도시 이름 입력" />
      <p>
        검색 중인 도시: <strong>{{ searchQuery }}</strong>
      </p>
    </section>

    <section class="list-box">
      <h3>🏙️ 지역별 날씨 현황</h3>

      <div v-for="item in filteredWeatherList" :key="item.id" class="weather-card" @click="selectedCityInfo = `${item.name}이 선택되었습니다.`">
        <h4>{{ item.name }} ({{ item.status }})</h4>
        <p>현재 기온: {{ item.temp }}°C</p>

        <span v-if="item.temp >= 25" class="badge hot">🔥 더움 (25도 이상)</span>
        <span v-else class="badge cool">❄️ 선선함 (25도 미만)</span>

        <button class="btn-detail" @click.stop="showDetail(item.name, item.status)">상세보기</button>
      </div>

      <p v-if="filteredWeatherList.length === 0" style="text-align: center; color: #e74c3c; padding: 10px 0">😭 검색 결과와 일치하는 도시가 없습니다.</p>
    </section>

    <div class="status-bar">
      {{ selectedCityInfo }}
    </div>
  </div>
</template>

<style scoped>
/* 전체 크기 계산 방식 통일 */
* {
  box-sizing: border-box;
}

/* 화면 전체 배경 */
.page-wrapper {
  width: 100%;
  min-height: 100vh;
  padding: 6px;
  background-color: #eaf5ff;
}

/* 전체 대시보드 */
.dashboard-wrapper {
  width: 100%;
  max-width: 580px;
  margin: 0 auto;
  padding: 30px 38px 20px;
  background-color: #ffffff;
  border: 1px solid #dce6ed;
  border-radius: 14px;
}

/* 상단 제목 */
.dashboard-header {
  margin-bottom: 18px;
  padding-bottom: 13px;
  border-bottom: 1px solid #e2e7eb;
}

.dashboard-header h2 {
  margin: 0;
  color: #203b57;
  font-size: 20px;
  font-weight: 700;
}

/* 검색 영역, 목록 영역 공통 */
.search-box,
.list-box {
  margin-bottom: 13px;
  padding: 13px;
  background-color: #f7f9fa;
  border: 1px solid #e1e5e8;
  border-radius: 7px;
}

/* 영역 제목 */
.search-box h3,
.list-box h3 {
  margin: 0 0 6px;
  color: #35536e;
  font-size: 14px;
  font-weight: 500;
}

/* 검색창 */
.search-box input {
  display: block;
  width: 100%;
  height: 30px;
  padding: 4px 8px;
  border: 1px solid #999999;
  background-color: #ffffff;
  color: #333333;
  font-size: 12px;
  outline: none;
}

/* 검색창 선택 시 */
.search-box input:focus {
  border-color: #5592cb;
  box-shadow: 0 0 0 2px rgba(85, 146, 203, 0.12);
}

/* 검색 중인 도시 문구 */
.search-box p {
  margin: 3px 0 0;
  color: #4c6880;
  font-size: 12px;
}

.search-box strong {
  color: #294b68;
}

/* 날씨 카드 */
.weather-card {
  position: relative;
  min-height: 85px;
  margin-bottom: 10px;
  padding: 11px 75px 10px 10px;
  background-color: #ffffff;
  border: 1px solid #d7dde2;
  border-radius: 5px;
  cursor: pointer;
  transition:
    border-color 0.15s ease,
    box-shadow 0.15s ease,
    transform 0.15s ease;
}

/* 마지막 카드 아래쪽 여백 제거 */
.weather-card:last-of-type {
  margin-bottom: 0;
}

/* 카드에 마우스를 올렸을 때 */
.weather-card:hover {
  transform: translateY(-1px);
  border-color: #a8c7e5;
  box-shadow: 0 3px 7px rgba(0, 0, 0, 0.07);
}

/* 도시 이름 */
.weather-card h4 {
  margin: 0 0 4px;
  color: #526b80;
  font-size: 13px;
  font-weight: 600;
}

/* 현재 기온 */
.weather-card p {
  margin: 0 0 5px;
  color: #526b80;
  font-size: 12px;
}

/* 온도 배지 공통 */
.badge {
  display: inline-block;
  padding: 4px 7px;
  border-radius: 3px;
  color: #ffffff;
  font-size: 10px;
  font-weight: 600;
}

/* 더움 */
.badge.hot {
  background-color: #ff6969;
}

/* 선선함 */
.badge.cool {
  background-color: #63b7f5;
}

/* 상세보기 버튼 */
.btn-detail {
  position: absolute;
  top: 12px;
  right: 10px;
  padding: 6px 9px;
  border: 1px solid #999999;
  background-color: #fafafa;
  color: #333333;
  font-size: 11px;
  cursor: pointer;
}

/* 버튼에 마우스를 올렸을 때 */
.btn-detail:hover {
  background-color: #eeeeee;
}

/* 버튼 클릭 시 */
.btn-detail:active {
  background-color: #dddddd;
}

/* 검색 결과 없음 */
.empty-message {
  margin: 0;
  padding: 13px 0;
  color: #e74c3c;
  font-size: 12px;
  text-align: center;
}

/* 하단 상태 표시줄 */
.status-bar {
  margin-top: 12px;
  padding: 11px;
  background-color: #edf8ec;
  border-radius: 5px;
  color: #26a13a;
  font-size: 12px;
  font-weight: 600;
  text-align: center;
}

/* 작은 화면 대응 */
@media (max-width: 600px) {
  .page-wrapper {
    padding: 4px;
  }

  .dashboard-wrapper {
    padding: 25px 20px 20px;
  }
}
</style>