<script setup>
import { computed } from 'vue'
import { useConfigStore } from '@/stores/configStore'

const configStore = useConfigStore()

// 구조분해 할당
const { cityItem } = defineProps({
  cityItem: {
    type: Object,
    required: true,
  },
})

const emit = defineEmits(['select-card', 'click-detail'])

// 전개 문법 + 옵셔널 체이닝
const normalizedCity = computed(() => ({
  ...cityItem,
  name: cityItem?.name ?? '도시 정보 없음',
  status: cityItem?.status ?? '상태 정보 없음',
  temp: Number(cityItem?.temp ?? 0),
}))

// 화씨/섭씨 변환
const displayTemp = computed(() => {
  const rawTemp = normalizedCity.value?.temp ?? 0

  if (configStore.unit === 'fahrenheit') {
    return Math.round((rawTemp * 9) / 5 + 32)
  }

  return rawTemp
})

// 카드 클릭
const handleCardClick = () => {
  const { name } = normalizedCity.value

  emit('select-card', `${name}이 선택되었습니다.`)
}

// 상세보기
const handleDetailClick = () => {
  const { name, status } = normalizedCity.value

  const args = [name, status]

  emit('click-detail', ...args)
}
</script>

<template>
  <div class="weather-card" @click="handleCardClick">
    <h4>
      {{ normalizedCity.name }}
      ({{ normalizedCity.status }})
    </h4>

    <p>
      현재 기온:
      {{ displayTemp }}{{ configStore.unitSymbol }}
    </p>

    <span
      v-if="normalizedCity.temp >= 25"
      class="badge hot"
    >
      🔥 더움
    </span>

    <span
      v-else
      class="badge cool"
    >
      ❄️ 선선함
    </span>

    <button
      class="btn-detail"
      @click.stop="handleDetailClick"
    >
      상세보기
    </button>
  </div>
</template>

<style scoped>
.weather-card {
  background: #fff;
  border: 1px solid #dee2e6;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 6px;
  cursor: pointer;
  position: relative;
}

.badge {
  display: inline-block;
  padding: 4px 8px;
  font-size: 12px;
  border-radius: 4px;
  color: #fff;
}

.hot {
  background-color: #ff7675;
}

.cool {
  background-color: #74b9ff;
}

.btn-detail {
  position: absolute;
  right: 12px;
  top: 15px;
  padding: 6px 10px;
  cursor: pointer;
}
</style>