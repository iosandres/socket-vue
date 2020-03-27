<template>
  <div>
    <div v-if="!connected">
    <b-form-input type="text" class="mb-2" v-model="username"></b-form-input>
    <b-button block variant="success" size="lg" @click="addUser">addUser</b-button>
    </div>
    <pre v-if="connected">Chat on</pre>
    <b-list-group>
    <b-list-group-item variant="primary" class="mb-2" v-for="(user, index) in listUsers" :key="index">{{user.username}}</b-list-group-item>
    </b-list-group>
    <div>
      
      <div class="window-chat">
        <p v-for="(msg,index) in messages" :key="index">
          {{msg.username}} : {{msg.message}}
          </p>
      </div>
      <div class="input-chat"  v-if="connected">
        <pre v-if="isTyping">{{userTyping}} is typing...</pre>
        <b-form-input type="text" class="mb-2" v-model="message"></b-form-input>
    <b-button block variant="success" size="lg" @click="sendMessage">Send</b-button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name:'chat',
  data(){
    return{
      username: '',
      userTyping:'',
      connected:false,
      listUsers:[],
      message:'',
      messages:[],
      isTyping:false
    }
  },
  methods:{
    addUser(){
      this.$socket.emit('add user', this.username)
    },
    sendMessage(){
      this.$socket.emit('newMessage', this.message)
       this.message = ''
    }
  },
  watch:{
    message(msg){
      if(msg.length>0){
        this.$socket.emit('typing',this.username)
      }else{
        this.$socket.emit('stopTyping', this.username)
      }
    }
  },
  sockets:{
    login(data){
      console.log(data)
      this.connected = data.status
    },
    userJoined(data){
      console.log(data)
      this.listUsers = data.listUsers
    },
    newMessage(data){
      this.messages.push(data)
    },
    typing(data){
      console.log("data typing:",data)
      this.userTyping = data.username
      this.isTyping = true
    },
    stopTyping(){
      this.isTyping = false
    }
  }
}
</script>
<style scoped>
</style>