<script setup>
import {onMounted, onUnmounted, ref} from "vue";

let messages = ref([])
let loading = ref(false)
let load = ref(false)
let offset = ref()
let messageText = ref('')
let styled = ref(false)

let savedMessages = ref([])

function getMessages(offset) {
  fetch(`https://numia.ru/api/getMessages?offset=${offset}`)
      .then(response => response.json())
      .then(data => {
        load.value = true
        data.result.forEach(el => {
          if (data.result.length !== 0) {
            messages.value.unshift(el)
            load.value = false
          } else return
        })
        return messages.value
      })
      .catch(error => {
        getMessages(offset)
        console.error(error + 'efsdjhuk')
      });
}
const sendMessage = () => {
  if (messageText.value.length !== 0) {
    window.scrollTo(0, 15)
    messages.value.unshift({val: messageText.value, isStyled: styled.value})
    savedMessages.value.push(messageText.value)
    sessionStorage.setItem('saved-messages', JSON.stringify(savedMessages.value));
    messageText.value = ''
  }
}
const loadMessages = (e) => {
  getMessages(offset.value)
  if (load.value === false) {
    window.scrollTo(0, 10);
    load.value = false
    offset.value = offset.value + 20
    return offset.value
  } else {
    e.preventDefault()
  }
}
const handleScroll = (e) => {
  let list = e.target.getElementById('message-list')
  if (list.getBoundingClientRect().top === 0 && load.value === false) {
    setTimeout(loadMessages(e), 2000);
  }
}
onMounted(() => {
  loading.value = true
  offset.value = 0
  load.value = true
  getMessages(0)
  setTimeout(() => {
    window.addEventListener("scroll", handleScroll)
    window.scrollTo(0, document.body.scrollHeight);
  }, 500);
  setTimeout(() => {
    loading.value = false
  }, 700);
})
onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll)
})
</script>

<template>
  <div v-if="loading === true" class="main-loader">
    <div class="loading"></div>
  </div>
  <div class="chat__container">
    <div class="chat__screen">
      <TransitionGroup id="message-list" ref="scrollComponent" class="chat__list" name="list" reverse tag="ul">
        <li v-for="item in messages" :key="item" :class="{'styled-message' : item.isStyled === false}">
          {{ item.val ? item.val : item }}
        </li>
      </TransitionGroup>
      <div class="chat__footer">
        <textarea v-model="messageText" type="text"/>
        <button @click="sendMessage">отправить</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* loaders */
.main-loader {
  position: fixed;
  height: 100vh;
  width: 100vw;
  top: 0;
  left: 0;
  z-index: 10;
  background: white;
  display: flex;
  align-items: center;
  justify-content: center;
}

.loading {
  height: 0;
  width: 0;
  padding: 15px;
  border: 6px solid #ccc;
  border-right-color: #888;
  border-radius: 22px;
  -webkit-animation: rotate 1s infinite linear;
}

@-webkit-keyframes rotate {
  100% {
    -webkit-transform: rotate(360deg);
  }
}

/* main */
.styled-message {
  background: #e2ffc7 !important;
}

.chat__container {
  background: #efe8de;
}

.chat__screen {
  margin-bottom: 100px;

}

.chat__list {
  list-style: none;
  height: 100%;
  padding: 40px;
  overflow: hidden;
}

.chat__list li {
  margin-top: 10px;
  border-radius: 10px;
  background: white;
  padding: 10px;
  width: auto;
}

.chat__footer {
  height: 100px;
  width: 100vw;
  background: #f0f0f0;
  position: fixed;
  display: flex;
  gap: 30px;
  align-items: center;
  justify-content: center;
  padding: 40px;
  bottom: 0;
}

.chat__footer textarea {
  width: 100%;
  height: 40px;
  border-radius: 20px;
  border: none;
  padding: 10px 25px 10px 25px;
  outline: none;
  resize: none;
  overflow: visible;
  cursor: pointer;
}

.chat__footer button {
  width: 160px;
  height: 40px;
  border-radius: 40px;
  border: none;
  color: white;
  font-weight: bold;
  text-transform: uppercase;
  padding: 10px 25px 10px 25px;
  cursor: pointer;
  background: #26c24a;
}

/* transition */
.list-move,
.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(-30px);
}
</style>
