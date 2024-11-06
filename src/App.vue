<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { toast } from "vue3-toastify";

import { computed, ref, onMounted } from "vue";

const transactions = ref([]);

onMounted(() => {
  const savedTransaction = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransaction) {
    transactions.value = savedTransaction;
  }
});

//Get Total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

//Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Get Expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

//Generate Uniqe Id
const generateUniqeId = () => {
  return Math.floor(Math.random() * 1000000);
};

//Add Transaction
const handleTransactionSumbitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqeId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  savedTransactionsToLocalStorage();

  if (transactionData.text && transactionData.amount) {
    toast("Transaction Added!", {
      theme: "colored",
      type: "success",
    });
  }
};

//Delete Transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transactions) => transactions.id !== id
  );

  savedTransactionsToLocalStorage();
  toast("Transaction Deleted!", {
    theme: "colored",
    type: "success",
  });
};

//Save to localstorage
const savedTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSumbitted" />
  </div>
</template>
