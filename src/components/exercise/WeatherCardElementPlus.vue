<script setup>
import { computed } from 'vue'
import { useConfigStore } from '@/stores/configStore'

const configStore = useConfigStore()

// 부모 컴포넌트로부터 도시 정보 전달받기
const { cityItem } = defineProps({
  cityItem: {
    type: Object,
    required: true,
  },
})

// 부모 컴포넌트로 전달할 이벤트 등록
const emit = defineEmits(['select-card', 'click-detail'])

// 설정된 온도 단위에 따라 섭씨 또는 화씨로 표시
const displayTemp = computed(() => {
  if (!cityItem) return 0

  const rawTemp = cityItem.temp

  if (configStore.unit === 'fahrenheit') {
    return Math.round((rawTemp * 9) / 5 + 32)
  }

  return rawTemp
})

// 온도에 따른 상태
const isHot = computed(() => {
  return cityItem.temp >= 25
})
</script>

<template>
  <el-card
    class="weather-card"
    shadow="hover"
    @click="emit('select-card', `${cityItem.name}이 선택되었습니다.`)"
  >
    <!-- 카드 제목 영역 -->
    <template #header>
      <div class="card-header">
        <div>
          <strong class="city-name">
            {{ cityItem.name }}
          </strong>

          <span class="weather-status">
            {{ cityItem.status }}
          </span>
        </div>

        <el-button
          type="primary"
          size="small"
          plain
          @click.stop="
            emit(
              'click-detail',
              cityItem.name,
              cityItem.status
            )
          "
        >
          상세보기
        </el-button>
      </div>
    </template>

    <!-- 카드 내용 영역 -->
    <div class="temperature-area">
      <span class="temperature-label">현재 기온</span>

      <strong class="temperature">
        {{ displayTemp }}{{ configStore.unitSymbol }}
      </strong>
    </div>

    <!-- 온도 상태 태그 -->
    <el-tag
      v-if="isHot"
      type="danger"
      effect="dark"
      round
    >
      🔥 더움
    </el-tag>

    <el-tag
      v-else
      type="primary"
      effect="dark"
      round
    >
      ❄️ 선선함
    </el-tag>
  </el-card>
</template>

<style scoped>
.weather-card {
  margin-bottom: 12px;
  cursor: pointer;
  transition:
    transform 0.2s,
    box-shadow 0.2s;
}

.weather-card:hover {
  transform: translateY(-3px);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.city-name {
  font-size: 18px;
}

.weather-status {
  margin-left: 8px;
  color: #909399;
  font-size: 14px;
}

.temperature-area {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.temperature-label {
  color: #606266;
}

.temperature {
  font-size: 24px;
  color: #303133;
}
</style>