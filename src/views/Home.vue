<template>
  <div class="container pt-5">
    <div class="row">
      <div class="col-md-4">
        <h3>List Users</h3>
        <ul class="list-group">
          <button v-for="(item, i) in users" :key="i" @click="getListChat(item.username)" class="list-group-item">
            {{item.username}}
          </button>
        </ul>
      </div>
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">
            {{to}}
          </div>
          <div class="card-body">
            <div v-for="(item, i) in chat" :key="i">
              {{item.from}} : {{item.msg}}
            </div>
          </div>
          <div class="card-footer">
            <form action="" class="form-inline" @submit.prevent="sendMsg()">
              <input v-model="text" type="text" class="form-control" placeholder="Text">
              <button class="btn btn-success ml-2">Send Message</button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import io from 'socket.io-client'

export default {
  data () {
    return {
      socket: io('http://localhost:4000'),
      text: '',
      users: [],
      chat: [],
      from: this.$route.query.username,
      to: ''
    }
  },
  name: 'Home',
  methods: {
    // test () {
    //   this.socket.emit('test', 'hello world')
    // },
    // kirimPesan () {
    //   const data = {
    //     username: this.username,
    //     text: this.text
    //   }
    //   this.socket.emit('pesan', data)
    //   this.text = ''
    // },
    // getPesan () {
    //   this.socket.on('response-pesan', (data) => {
    //     console.log(data)
    //     this.content = [...this.content, data]
    //   })
    // }
    joinRoom () {
      this.socket.emit('join-room', this.from)
    },
    getListUsers () {
      this.socket.emit('get-list-users', this.from)
    },
    resGetListUsers () {
      this.socket.on('res-get-list-users', (users) => {
        this.users = users
      })
    },
    getListChat (username) {
      this.to = username
      this.socket.emit('get-list-chat', { from: this.from, to: username })
      // this.resGetListChat()
    },
    resGetListChat () {
      this.socket.on('res-get-list-chat', (chat) => {
        this.chat = chat
      })
    },
    sendMsg () {
      const data = {
        from: this.from,
        to: this.to,
        msg: this.text
      }
      this.socket.emit('send-message', data)
      this.text = ''
    }
  },
  mounted () {
    this.joinRoom()
    // this.test()
    // this.getPesan()
    this.getListUsers()
    this.resGetListUsers()
    this.resGetListChat()
  }
}
</script>
