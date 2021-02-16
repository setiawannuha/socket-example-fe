<template>
  <div class="container pt-5">
    <div class="card">
      <div class="card-body">
        <form action="" class="form-inline" @submit.prevent="onLogin()">
          <input type="text" v-model="username" class="form-control" placeholder="Username">
          <button class="btn btn-primary ml-2">Login</button>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import io from 'socket.io-client'
export default {
  data () {
    return {
      username: '',
      socket: io('http://localhost:4000')
    }
  },
  methods: {
    onLogin () {
      this.socket.emit('login', this.username)
    }
  },
  mounted () {
    this.socket.on('res-login', (status) => {
      if (status) {
        this.$router.push(`/home?username=${this.username}`)
      } else {
        alert('user not found')
      }
    })
  }
}
</script>
