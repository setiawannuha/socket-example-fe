<template>
  <div class="container pt-5">
    <div class="row">
      <div class="col-md-4">
        <h3>Login : {{user_name}} ({{room_id}})</h3>
        <ul class="list-group">
          <button v-for="(item, i) in users" :key="i" @click="getListChat(item.id, item.name)" class="list-group-item">
            {{item.name}}
          </button>
        </ul>
      </div>
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">
            {{to}}
          </div>
          <div class="card-body">
            <div class="mb-4" v-for="(item, i) in chat" :key="i">
              <div v-if="item.from_name === user_name" class="text-right">
                {{item.from_name}} : {{item.message}}
                <div>
                  <small class="">{{item.created_at}}</small>
                </div>
              </div>
              <div v-else>
                {{item.from_name}} : {{item.message}}
                <div>
                  <small class="">{{item.created_at}}</small>
                </div>
              </div>
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
    <div class="row mt-5 pt-5">
      <div class="col-md-12">
        <div class="card">
          <div class="card-header">
            <h4>Pengumuman</h4>
            <form action="" class="form-inline" @submit.prevent="sendBroadcast()">
              <input v-model="textBroadcast" type="text" class="form-control" placeholder="Text">
              <button class="btn btn-success ml-2">Send Broadcast</button>
            </form>
          </div>
          <div class="card-body">
            <div v-for="(item, i) in listBroadcast" :key="i">
              {{item}}
            </div>
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
      user_name: this.$route.query.name,
      room_id: this.$route.query.room_id,
      socket: io('http://localhost:4000'),
      text: '',
      users: [],
      chat: [],
      from: this.$route.query.id_user,
      to: '',
      to_id: '',
      textBroadcast: '',
      listBroadcast: []
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
      this.socket.emit('join-room', this.room_id)
    },
    getListUsers () {
      this.socket.emit('get-list-users', this.from, this.room_id)
    },
    resGetListUsers () {
      this.socket.on('res-get-list-users', (users) => {
        this.users = users
      })
    },
    getListChat (idUserTujuan, nama) {
      this.to = nama
      this.to_id = idUserTujuan
      this.socket.emit('get-list-chat', { id_from: this.from, id_to: idUserTujuan, room_id: this.room_id })
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
        to: this.to_id,
        msg: this.text
      }
      this.socket.emit('send-message', data)
      this.text = ''
    },
    sendBroadcast () {
      const data = {
        from: this.from,
        msg: this.textBroadcast
      }
      this.socket.emit('send-broadcast', data)
      this.textBroadcast = ''
    },
    resBroadcast () {
      this.socket.on('res-broadcast', (data) => {
        this.listBroadcast = [...this.listBroadcast, data]
      })
    }
  },
  mounted () {
    this.joinRoom()
    // this.test()
    // this.getPesan()
    this.getListUsers()
    this.resGetListUsers()
    this.resGetListChat()
    this.resBroadcast()
  }
}
</script>
