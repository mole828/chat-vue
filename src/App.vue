<template>
<div style="max-width: 500px;">
  <div class="q-pa-md row justify-center">
    <div style="width: 100%; max-width: 400px">
      <q-chat-message
        v-for="(msg,key) in newList"
        :key="key"
        :sent="msg.sent"
        :name="String(msg.name)"
        :text="[String(msg.val)]"
      />
    </div>
  </div>
  <q-input outlined v-model="input" v-on:keyup.enter="send()" label="Outlined" />
</div>
  
</template>

<script>
// import { } from 'vue'
import io from 'socket.io-client'
import { ref, toRefs, reactive } from 'vue';
// import vueSocketIo from 'vue-socket.io'
export default {
  name: 'App',
  components: {
  },
  setup(){
    const socket = io('ws://localhost:7001/chat',{})
    let name = undefined;
    let state = reactive({
      newList:[]
    })
    socket.on('msg',(data)=>{
      data.sent = data.name === name;
      // console.log('name',state.name)
      // console.log(data);
      state.newList.push(data);
      // console.log(state.newList);
    })
    socket.on('connect', () => {
        name = socket.id;
        // socket.emit('join','conn');
        // console.log('#connect,', name, socket);
    });
    let input = ref('');
    let send = ()=>{
      //console.log(input)
      socket.emit('msg',input.value);
      input.value='';
    }
    return {
      input,
      send,
      ...toRefs(state),
    }
  }
}
</script>

<style>
</style>
