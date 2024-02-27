<template>
  <Header />
  <div class="container">
    <Balance :total="total"/>
    <IncomeExpenses :income="income" :expenses="expenses"/>
    <TransactionList :transactions="transactions" @transaction-deleted="handleTransactionDeleted"/>
    <AddTransaction @transaction-submitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { useToast } from 'vue-toastification';

import { ref, computed } from 'vue';

const toast = useToast();

const transactions = ref([
  { id: 1, text: 'Flower', amount: -19.99 },
  { id: 2, text: 'Salary', amount: 299.97 },
  { id: 3, text: 'Book', amount: -10 },
  { id: 4, text: 'Camera', amount: 150 }
]);

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

//Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount    
  });
  toast.success('Transaction added successfully!');
  console.log(transactions.value);
};

//generate unique id
const generateUniqueId = () => {
  const lastTransaction = transactions.value[transactions.value.length - 1];
  return lastTransaction.id + 1;
};

//Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(transaction => transaction.id !== id);
  toast.success('Transaction deleted successfully');
};
</script>
