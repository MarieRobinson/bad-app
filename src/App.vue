<template>
  <Header />
  <div class="container">
    <Balance :total="total"/>
    <IncomeExpenses :income="+income" :expenses="+expenses"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';


import { ref, computed, onMounted } from 'vue';

import { useToast } from 'vue-toastification';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// get total
const total = computed(() => {
  return parseInt(transactions.value
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2));
});


// get income
const income = computed(() => {
  return parseInt(transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2));
});

// get expenses
const expenses = computed(() => {
  return parseInt(transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2));
});

//Submit transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount    
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction added.');
};

//generate unique id
const generateUniqueId = () => {
  if (transactions.value.length === 0) {
    return 1;
  }
  const lastTransaction = transactions.value[transactions.value.length - 1];
  return lastTransaction.id + 1;
};

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    transaction => transaction.id !== id
  );

  saveTransactionsToLocalStorage();

  toast.success('Transaction deleted.');
};

//save to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
