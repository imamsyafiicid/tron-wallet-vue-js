<template>
  <div>
    <h2>Transfer TRX</h2>
    <input v-model="toAddress" placeholder="Receiver Wallet Address" />
    <input v-model="amount" type="number" placeholder="Amount to Transfer" />
    <button @click="transferTrx">Send TRX</button>
    <p v-if="!tronWeb">TronLink wallet not detected. Please install TronLink.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// State
const toAddress = ref('');
const amount = ref(0);
let tronWeb = window.tronWeb;

// Initialize TronWeb when component is mounted
onMounted(() => {
  if (!window.tronWeb || !window.tronWeb.ready) {
    console.warn('TronLink wallet is not available. Please install TronLink.');
  } else {
    console.log('TronLink is available!');
    tronWeb = window.tronWeb; // Use TronLink's tronWeb instance
  }
});

// Transfer TRX function
const transferTrx = async () => {
  if (!tronWeb) {
    console.error('TronLink wallet is not available.');
    return;
  }

  try {
    const transaction = await tronWeb.transactionBuilder.sendTrx(
      toAddress.value,
      tronWeb.toSun(amount.value), // Convert amount to Sun (smallest TRX unit)
      tronWeb.defaultAddress.base58
    );
    const signedTransaction = await tronWeb.trx.sign(transaction);
    const broadcast = await tronWeb.trx.sendRawTransaction(signedTransaction);
    console.log('Transaction Result:', broadcast);
  } catch (error) {
    console.error('Transaction Error:', error);
  }
};
</script>

<style scoped>
h2 {
  color: #42b983;
}
input {
  margin-bottom: 10px;
  padding: 8px;
  border: 1px solid #ddd;
}
button {
  padding: 8px 16px;
  background-color: #42b983;
  color: white;
  border: none;
  cursor: pointer;
}
</style>
