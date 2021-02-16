<template>
  <div class="container pt-5">
    <div class="card">
      <div class="card-body">
        <form action="" @submit.prevent="onLogin()">
          <input type="text" v-model="name" class="form-control w-50 mt-3" placeholder="Name">
          <input type="text" v-model="password" class="form-control w-50 mt-3" placeholder="Password">
          <button class="btn btn-primary mt-4">Login</button>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import axios from 'axios'
export default {
  data () {
    return {
      name: '',
      password: ''
    }
  },
  methods: {
    onLogin () {
      axios.post('http://localhost:4000/login', { name: this.name, password: this.password }).then((response) => {
        this.$router.push(`/home?room_id=${response.data[0].room_id}&id_user=${response.data[0].id}&name=${response.data[0].name}`)
      }).catch((err) => {
        alert(err.response.data.msg)
      })
    }
  }
}
</script>
