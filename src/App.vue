<template>
  <AppHeader />
  <div class="container">
    <AppBalance :total="+total" />
    <AppIncomeExpenses :income="+income" :expenses="+expenses" />
    <AppTransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted" />
    <AppAddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import { useToast } from 'vue-toastification';
import AppAddTransaction from './components/AppAddTransaction.vue';
import AppBalance from './components/AppBalance.vue';
import AppHeader from './components/AppHeader.vue';
import AppIncomeExpenses from './components/AppIncomeExpenses.vue';
import AppTransactionList from './components/AppTransactionList.vue';

import { computed, ref } from 'vue';

const toast = useToast();

const transactions = ref([
  { id: 1, text: 'Flower', amount: -20 },
  { id: 2, text: 'Salary', amount: 300 },
  { id: 3, text: 'Book', amount: -10 },
  { id: 4, text: 'Camera', amount: 150 }
]);

//Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

//Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2)
});

//Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2)
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });
  toast.success('Transaction added');
}

//Generate unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
}

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id;
  })
  toast.success('Transaction deleted');
}
</script>


<style scoped></style>
