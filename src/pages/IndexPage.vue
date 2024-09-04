<template>
  <q-page padding>
    <q-form @submit="addEntry">
      <div class="row q-gutter-md">
        <q-input class="col" v-model="date" label="날짜" outlined />
        <q-input class="col" v-model="description" label="내용" outlined />
        <q-input class="col" v-model="income" label="입금" type="number" outlined />
        <q-input class="col" v-model="expense" label="출금" type="number" outlined />
      </div>
      <div class="row q-gutter-md">
        <q-btn class="col" label="추가" color="primary" type="submit" />
      </div>
    </q-form>

    <q-table
      :rows="entries"
      :columns="columns"
      row-key="id"
      class="q-mt-md"
    />

    <!-- 데이터 보기 버튼 -->
    <q-btn label="데이터보기" color="secondary" @click="toggleShowData" class="q-mt-md" />

    <!-- JSON 데이터 표시 -->
    <div v-if="showData" class="q-mt-md">
      <pre>{{ entriesAsJson }}</pre>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed } from 'vue';

// 오늘 날짜를 기본 값으로 설정하고 'YYYYMMDD' 형식으로 변환
const today = () => {
  const now = new Date();
  const year = now.getFullYear();
  const month = String(now.getMonth() + 1).padStart(2, '0'); // 월을 2자리로
  const day = String(now.getDate()).padStart(2, '0'); // 일을 2자리로
  return `${year}${month}${day}`;
};

const date = ref(today());
const description = ref('');
const income = ref(0);
const expense = ref(0);
const entries = ref([
  { 'date': '20240905', 'description': '월급', 'income': 1000000, 'expense': 0 }
]);

// 테이블 컬럼 정의
const columns = [
  { name: 'date', label: '날짜', field: 'date', format: (val, row) => { return formatDateForDisplay(val); }, align: 'left', sortable: true },
  { name: 'description', label: '내용', field: 'description', align: 'left' },
  { name: 'income', label: '입금', field: 'income', format: (val, row) => { return val.toLocaleString(); }, align: 'right' },
  { name: 'expense', label: '출금', field: 'expense', format: (val, row) => { return val.toLocaleString(); }, align: 'right' }
];

// 날짜 형식을 'YYYY-MM-DD'로 변환
const formatDateForDisplay = (dateStr) => {
  return `${dateStr.slice(0, 4)}-${dateStr.slice(4, 6)}-${dateStr.slice(6, 8)}`;
};

// 항목 추가 함수
const addEntry = () => {
  if (date.value && (income.value || expense.value)) {
    entries.value.push({
      id: Date.now(),
      date: date.value, // 'YYYYMMDD' -> 'YYYY-MM-DD'로 변환
      description: description.value,
      income: parseFloat(income.value) || 0,
      expense: parseFloat(expense.value) || 0
    });

    // 입력 필드 초기화 (날짜는 다시 오늘 날짜로 설정)
    date.value = today();
    description.value = '';
    income.value = 0;
    expense.value = 0;
  }
};

// JSON 데이터를 표시할지 여부를 위한 상태
const showData = ref(false);

// JSON 데이터를 보기 위한 함수
const toggleShowData = () => {
  showData.value = !showData.value;
};

// entries 배열을 JSON 형식으로 변환
const entriesAsJson = computed(() => {
  return JSON.stringify(entries.value, null, 2);
});


</script>

<style>
.q-page {
  margin: 0 auto;
}

.row + .row {
  margin-top: 1rem;
}
</style>
