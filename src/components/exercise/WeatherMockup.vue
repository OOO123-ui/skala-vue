<script setup>
import { ref } from 'vue'

// 4일차 API 연동을 대비한 가상의 백엔드 데이터 배열 (v-for 및 :key 실습용)
const weatherList = ref([
  { id: 'city_01', name: '서울', temp: 28, status: '맑음' },
  { id: 'city_02', name: '수원', temp: 24, status: '비' },
  { id: 'city_03', name: '부산', temp: 26, status: '구름' },
])

// 검색어 및 알림창 제어용 데이터 (v-model 대용 한글 처리 및 이벤트 실습용)
const searchQuery = ref('')
const selectedCityInfo = ref('카드를 클릭하거나 검색해 보세요.')

// 알림 대행 함수 (window 객체 격리 우회)
const showDetail = (cityName, status) => {
  window.alert(`${cityName}의 현재 날씨는 [${status}] 상태입니다.`)
}
</script>

<template>
  <div class="dashboard-wrapper">
    <section class="search-box">
      <h3>🔍 도시 검색</h3>
      <!-- input type="text" v-model="searchQuery" placeholder="검색할 도시 이름 입력" / -->
      <input type="text" :value="searchQuery" @input="(e) => (searchQuery = e.target.value)" placeholder="검색할 도시 이름 입력" />
      <p>
        검색 중인 도시: <strong>{{ searchQuery }}</strong>
      </p>
    </section>

    <section class="list-box">
      <h3>🏙️ 지역별 날씨 현황</h3>

      <div v-for="item in weatherList" :key="item.id" class="weather-card" @click="selectedCityInfo = `${item.name}이 선택되었습니다.`">
        <h4>{{ item.name }} ({{ item.status }})</h4>
        <p>현재 기온: {{ item.temp }}°C</p>

        <span v-if="item.temp >= 25" class="badge hot">🔥 더움 (25도 이상)</span>
        <span v-else class="badge cool">❄️ 선선함 (25도 미만)</span>

        <button class="btn-detail" @click.stop="showDetail(item.name, item.status)">상세보기</button>
      </div>
    </section>

    <div class="status-bar">
      {{ selectedCityInfo }}
    </div>
  </div>
</template>

<style scoped>
.dashboard-wrapper {
  width: calc(100% - 16px);
  max-width: 580px;
  margin: 6px auto;
  padding: 32px 38px 28px;
  background-color: white;
  border: 1px solid #dce7ef;
}

/* 상단 제목 */
.dashboard-header {
  margin-bottom: 20px;
  padding-bottom: 14px;
  border-bottom: 1px solid #e4e7eb;
}

.dashboard-header h2 {
  margin: 0;
  color: #20364f;
  font-size: 20px;
  font-weight: 700;
}

/* 검색 영역과 날씨 목록 영역 */
.search-box,
.list-box {
  margin-bottom: 13px;
  padding: 14px;
  background-color: #f8f9fa;
  border: 1px solid #e5e8eb;
  border-radius: 7px;
}

.search-box h3,
.list-box h3 {
  margin: 0 0 5px;
  color: #31516e;
  font-size: 14px;
  font-weight: 500;
}

/* 검색창 */
.search-box input {
  display: block;
  width: 100%;
  height: 29px;
  padding: 4px 8px;
  border: 1px solid #999;
  background-color: white;
  font-size: 12px;
}

.search-box p {
  margin: 2px 0 0;
  color: #48647e;
  font-size: 12px;
}

/* 날씨 카드 */
.weather-card {
  position: relative;
  min-height: 85px;
  margin-bottom: 10px;
  padding: 11px 72px 10px 10px;
  background-color: white;
  border: 1px solid #d9dee3;
  border-radius: 5px;
  cursor: pointer;
}

.weather-card:last-child {
  margin-bottom: 0;
}

.weather-card:hover {
  border-color: #a8c7e6;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.08);
}

/* 도시명 */
.weather-card h4 {
  margin: 0 0 4px;
  color: #52687c;
  font-size: 13px;
  font-weight: 600;
}

/* 현재 기온 */
.weather-card p {
  margin: 0 0 5px;
  color: #52687c;
  font-size: 12px;
}

/* 온도 배지 */
.badge {
  display: inline-block;
  padding: 4px 7px;
  border-radius: 3px;
  color: white;
  font-size: 10px;
  font-weight: 600;
}

.badge.hot {
  background-color: #ff6868;
}

.badge.cool {
  background-color: #62b8f7;
}

/* 상세보기 버튼 */
.btn-detail {
  position: absolute;
  top: 12px;
  right: 10px;
  padding: 6px 9px;
  border: 1px solid #999;
  background-color: #f8f8f8;
  color: #333;
  font-size: 11px;
  cursor: pointer;
}

.btn-detail:hover {
  background-color: #eeeeee;
}

/* 아래 상태 표시줄 */
.status-bar {
  margin-top: 12px;
  padding: 11px;
  background-color: #eef9ed;
  border-radius: 5px;
  color: #2da23a;
  font-size: 12px;
  font-weight: 600;
  text-align: center;
}

@media (max-width: 600px) {
  .dashboard-wrapper {
    padding: 25px 20px;
  }
}
</style>