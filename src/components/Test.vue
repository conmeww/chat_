<script setup>
import {onMounted, onUnmounted, ref} from "vue";

let messages = ref([])
let loading = ref(false)
let scrollComponent = ref(null)

const fetchMessages = fetch(
    `https://numia.ru/api/getMessages?offset=20`
);
// Using Promise syntax:
function getData() {
  fetch('https://numia.ru/api/getMessages?offset=20')
      .then(response => response.json())
      .then(data => {
        console.log(data);
      })
      .catch(error => {
        getData()
        console.error(error + 'efsdjhuk')
      });
}

</script>
<template>
  <button @click="getData()">GET</button>
</template>