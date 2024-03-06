<template>
  <Header></Header>

  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpenses :income="+income" :expenses="+expenses"></IncomeExpenses>
    <TransactionList
      @transactionDeleted="handleTransactionDeleted"
      :transactions="transactions"
    ></TransactionList>
    <AddTransaction
      @transactionSubmitted="handleTransactionSubmitted"
    ></AddTransaction>
  </div>
</template>
<script setup>
import { useToast } from "vue-toastification";

import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { ref, computed, onMounted } from "vue";
const toast = useToast();
const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transactions) => {
      return acc + transactions.amount;
    }, 0);
});
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transactions) => {
      return acc + transactions.amount;
    }, 0);
});

const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  saveTransactionsToLocalStorage();
  toast.success("prodotto aggiunto");
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactionsToLocalStorage();
  toast.success("cancellato con successo");
};

const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000);
};
</script>
