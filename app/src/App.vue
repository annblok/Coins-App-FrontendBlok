<script setup lang="ts">
 import { ref, computed, watch } from 'vue';

 interface Expense {
  category: string;
  amount: number;
 }

 const income = ref<number>(Number(localStorage.getItem('income')) || 0);
 const goal = ref<number>(Number(localStorage.getItem('goal')) || 0);
 const expenses = ref<Expense[]>(
  JSON.parse(localStorage.getItem('expenses') || "[]")
 );

 const addExpense = () => {
  expenses.value.push({
    category: '',
    amount: 0
  })
 };

 const removeExpense = (index: number) => {
  expenses.value.splice(index, 1);
 };

 const totalExpenses = computed(() =>
  expenses.value.reduce((sum, exp) => sum + (exp.amount || 0), 0)
);

 const formattedIncome = computed(() =>
  new Intl.NumberFormat('ru-RU').format(income.value)
 );
 const formattedExpense = computed(() =>
  new Intl.NumberFormat('ru-RU').format(totalExpenses.value)
 );
 const formattedGoal = computed(() =>
  new Intl.NumberFormat('ru-RU').format(goal.value)
 );

 const formattedBalance = computed(() =>
  new Intl.NumberFormat('ru-RU').format(balance.value)
 );

 const balance = computed(() => income.value - totalExpenses.value);

 const goalProgress = computed(() => {
  if (goal.value <= 0) return 0;
  const progress = Math.floor((balance.value / goal.value) * 100);
  return progress < 0 ? 0 : progress > 100 ? 100 : progress;
 });

 watch(income, (newVal) => {
  localStorage.setItem('income', newVal.toString());
 });

 watch(goal, (newVal) => {
  localStorage.setItem('goal', newVal.toString());
 });

 watch(expenses, (newVal) => {
  localStorage.setItem('expenses', JSON.stringify(newVal));
 }, { deep: true }
);

</script>

<template>
  <div class="budget-app">
    <div class="top">
      <section class="section income">
        <h2>Доходы</h2>
        <div class="section-box"><input type="number" v-model.number="income"></div>
      </section>
      <section class="section dream">
        <h2>Цель на мечту</h2>
        <div class="section-box"><input type="number" v-model.number="goal"></div>
      </section>
    </div>
    <section class="section expenses">
        <h2>Расходы</h2>
        <div v-for="(expense, index) in expenses" :key="index" class="expense-item">
          <select v-model="expense.category">
            <option disabled value="">Выберите категорию</option>
            <option value="Питание">Питание</option>
            <option value="Здоровье">Здоровье</option>
            <option value="Питание">Аренда</option>
            <option value="Здоровье">Такси</option>
            <option value="Питание">Одежда</option>
            <option value="Здоровье">Прочее</option>
          </select>
          <input type="number" v-model.number="expense.amount">
          <button @click="removeExpense(index)">&#x2715;</button>
        </div>
        <button @click="addExpense">Добавить расход</button>
    </section>
    <section class="section summary">
        <h2>Итоги</h2>
        <p>Доходы: {{ formattedIncome }} ₽</p>
        <p>Расходы: {{ formattedExpense }} ₽</p>
        <p>Остаток: {{ formattedBalance }} ₽</p>
        <p>Цель на мечту: {{ formattedGoal }} ₽</p>
        <p>Прогресс: {{ goalProgress }}%</p>
    </section>
  </div>
</template>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Sansation:ital,wght@0,300;0,400;0,700;1,300;1,400;1,700&display=swap');
  body {
  font-family: "Sansation", sans-serif;
  color: #3c7741;
  background-color: #caf6e4;
  }
  .budget-app {
  font-family: "Sansation", sans-serif;
  max-width: 600px;
  margin: 0 auto;
  }
  h1 {
  text-align: center;
  margin: 0;
  }
  h2 {
  margin-top: 0;
  margin-bottom: 10px;
  }


  .top {
  display: flex;
  gap: 10px;
  margin-top: 30px;
  }
  .section {
  flex-grow: 1;
  padding: 20px 30px;
  border-radius: 10px;
  background-color: #caf6e4;
  border: 1px solid #51b9a1;
  margin-bottom: 10px;
  }
  .section-box > input {
  font-family: "Sansation", sans-serif;
  margin-top: 8px;
  border: 1px solid #51b9a1;
  border-radius: 6px;
  height: 40px;
  padding: 0 10px;
  box-sizing: border-box;
  }
  .expense-item {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 10px;
  }


  .expense-item > input,
  .expense-item > select {
  flex-grow: 1;
  font-family: "Sansation", sans-serif;
  border: 1px solid #51b9a1;
  border-radius: 6px;
  height: 40px;
  padding: 0 10px;
  box-sizing: border-box;
  }


  .expense-item > input + button {
  padding: 0;
  background-color: transparent;
  }
  select, input {
  padding: 0.4rem;
  }
  input::-webkit-outer-spin-button,
  input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;
  }
  input:focus {
  outline: none;
  border: 1px solid #145646;
  }
  button {
  font-family: "Sansation", sans-serif;
  text-transform: uppercase;
  font-weight: 900;
  cursor: pointer;
  color: #1b401e;
  background-color: #51b9a1;
  border: none;
  border-radius: 6px;
  height: 40px;
  padding: 0 20px;
  box-sizing: border-box;
  }
  button:hover {
  background-color: #67cdb5;
  }
</style>
