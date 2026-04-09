<script setup>
import { computed, onBeforeUnmount, ref, watch } from 'vue'
import Select from 'primevue/select'
import FloatLabel from 'primevue/floatlabel'

const MONTH_NAMES = [
  'січень',
  'лютий',
  'березень',
  'квітень',
  'травень',
  'червень',
  'липень',
  'серпень',
  'вересень',
  'жовтень',
  'листопад',
  'грудень',
]

const SEASON_BY_MONTH = [
  'зима',
  'зима',
  'весна',
  'весна',
  'літо',
  'літо',
  'літо',
  'літо',
  'осінь',
  'осінь',
  'осінь',
  'зима',
]

const SEASON_TEXT = {
  зима: 'То є холодна й люта',
  весна: 'То є квітуча й тепла (дай бог)',
  літо: 'То є жарке',
  осінь: 'То є падає листячко й ллють дощі',
}

const months = MONTH_NAMES.map((name, i) => {
  const id = i + 1
  const season = SEASON_BY_MONTH[i]
  return {
    id,
    name,
    season,
    seasonText: SEASON_TEXT[season],
  }
})

const selectedMonth = ref(null)
const countdown = ref(null)
const showMonthMessage = ref(false)

let countdownInterval = null

function clearCountdown() {
  if (countdownInterval !== null) {
    clearInterval(countdownInterval)
    countdownInterval = null
  }
}

function startCountdown() {
  clearCountdown()
  showMonthMessage.value = false
  countdown.value = 3

  countdownInterval = setInterval(() => {
    if (countdown.value > 1) {
      countdown.value -= 1
      return
    }
    clearCountdown()
    countdown.value = null
    showMonthMessage.value = true
  }, 1000)
}

const selectedMonthData = computed(() =>
  months.find((m) => m.id === selectedMonth.value),
)

watch(selectedMonth, (id) => {
  if (id === null) return
  startCountdown()
})

onBeforeUnmount(clearCountdown)
</script>

<template>
  <div class="page">
    <header>
      <h1>✨Спешиал фор ю✨</h1>
    </header>

    <main>
      <h3>Для моєї забудькуватої пані, яка не знає номери місяців</h3>
      <div class="grid">
        <FloatLabel class="picker" variant="on">
          <Select
            id="months"
            v-model="selectedMonth"
            :options="months"
            option-label="id"
            option-value="id"
            show-clear
            class="month-select"
          />
          <label for="months">Номер місяця:</label>
        </FloatLabel>

        <section class="message">
          <h3 v-if="countdown !== null" class="countdown">
            {{ countdown }}...
          </h3>

          <template v-else-if="showMonthMessage && selectedMonthData">
            <p>
              {{ selectedMonthData.seasonText }}
              <strong>{{ selectedMonthData.season }}</strong>
            </p>
            <p>
              А місяць це <strong>{{ selectedMonthData.name }}</strong>
            </p>
          </template>

          <p v-else>Що ж то буде за місяць 🤔</p>
        </section>
      </div>
    </main>
  </div>
</template>

<style scoped>
.page {
  --header-height: 50px;
}

header {
  display: flex;
  justify-content: center;
  align-items: center;
  height: var(--header-height);
}

main {
  height: calc(100vh - var(--header-height));
  margin-top: 32px;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.grid {
  display: grid;
  grid-template-columns: 1fr;
  gap: 16px;
}

.picker {
  width: 100%;
}

.month-select {
  width: 100%;
}

.countdown {
  text-align: center;
}

@media (min-width: 768px) {
  .page {
    --header-height: 100px;
  }

  .grid {
    grid-template-columns: 1fr 1fr;
  }

  .message {
    justify-self: center;
    align-self: center;
  }
}
</style>
