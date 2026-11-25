<template>
  <AppHeader />
  <div class="container">
    <AppBalance :total="total" />
    <AppIncomeExpenses :income="income" :expenses="expenses" />
    <AppTransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted" />
    <AppAddTransaction @transaction-submitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import { useToast } from 'vue-toastification';
import AppAddTransaction from './components/AppAddTransaction.vue';
import AppBalance from './components/AppBalance.vue';
import AppHeader from './components/AppHeader.vue';
import AppIncomeExpenses from './components/AppIncomeExpenses.vue';
import AppTransactionList from './components/AppTransactionList.vue';

import { computed, onMounted, ref } from 'vue';

const toast = useToast();

const transactions = ref([]);

//Check localStorage
onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
})

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
    }, 0);
});

//Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
});

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction added');
}

//Generate unique ID
const generateUniqueId = () => {
  return Date.now();
}

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id;
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction deleted');
}

//Save to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}
</script>

<style scoped></style>
