<template>
  <Header />
  <div class="container">

    <Balance :total="total" />
    <IncomeExpenses :income="income" :expenses="expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleDelete" />
    <AddTransaction @transactionSubmitted="handleSubmit" />
  </div>

</template>

<script setup>
import Header from './components/Header.vue'
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'
import { ref, computed, onMounted } from 'vue'
import { useToast } from 'vue-toastification'


const toast = useToast()

const transactions = ref([])

// localstorage fetch

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('vue:exptracker:transactions'))
  if (savedTransactions) {
    transactions.value = savedTransactions
  }
})

// total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

// Income
const income = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount > 0).reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

// expenses

const expenses = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount < 0).reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0)
})

// handle submit

const handleSubmit = (transactionData) => {
  const dataToSubmit = {
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount
  }
  transactions.value.push(dataToSubmit)
  saveTransactionToLocalStorage()
  toast.success('Added')
}

// generate Unique id

const generateUniqueId = () => {
  return Math.floor(Math.random() * 10000)
}

// handle delete

const handleDelete = (id) => {
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionToLocalStorage()
  toast.success('deleted')
}

// save transaction to localStorage

const saveTransactionToLocalStorage = () => {
  localStorage.setItem('vue:exptracker:transactions', JSON.stringify(transactions.value))
}

</script>