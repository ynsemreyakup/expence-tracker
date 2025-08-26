<template>

 <Header />
  <div class="container">
     <Balance :total="total" />
     <IncomeExpense :income= "+income" :expenses="+expenses" />
     <TransactionList :transactions=" transactions" @transactionDeleted ="handleTransactionDeleted"/>
     <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />

  </div>

</template>

<script setup>

import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpense from './components/IncomeExpense.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { useToast } from 'vue-toastification'
const toast = useToast()

import {ref, computed, onMounted} from 'vue';



onMounted(() =>{

const savedTransactions= JSON.parse(localStorage.getItem('transactions'));

if(savedTransactions) {

  transactions.value = savedTransactions;
}
});
 

const transactions = ref([
              { id: 1, text :'Flower ', amount : -19.99} ,
              { id: 2, text :'Salary', amount : -299.97} ,
              { id: 3, text :'Book', amount : -10} ,
              { id: 4, text :'Camera', amount : 1780} ,
]);

//get total
const total = computed(()=>{
  return transactions.value.reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0);

});


// get income
const income = computed( () => {
  return transactions.value
  .filter((transaction)  => transaction.amount >0 )
  .reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0)

  .toFixed(2);

 });


 // get expense 
const expenses = computed(()=> {
  return transactions.value
  .filter((transaction)  => transaction.amount < 0 )
  .reduce((acc, transactions) => {
    return acc + transactions.amount;
  }, 0)
  .toFixed(2);
  
 });

 //add transaction

 const handleTransactionSubmitted = (transactionData) => {

  transactions.value.push({
    id: generateUniqueId(),
    text:  transactionData.text,
    amount: transactionData.amount,

  });
     
  saveTransactionsToLocalStorage();   


  toast.success('Transaction added');


 };

  const generateUniqueId = () => {

    return Math.floor(Math.random() * 100000)
  };



  //delete

 const handleTransactionDeleted = (id) => { 
    transactions.value = transactions.value.filter( 
      (transaction) => transaction.id !== id
    );
   saveTransactionsToLocalStorage();
    toast.success('Transaction deleted');
 };


 //save localstorrage

 const saveTransactionsToLocalStorage = () => {
   localStorage.setItem('transactions', JSON.stringify(transactions.value));

 };


</script>