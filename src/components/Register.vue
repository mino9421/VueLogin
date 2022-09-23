<script>
  import PocketBase from 'pocketbase';

  const client = new PocketBase('http://127.0.0.1:8090');

  export default {

    data () {
      return {
        registerEmail: '',
        registerPassword: '',
        email: '',
        password: '',
        status: false,

      }
    },

    methods: {
      async register () {
        const user = await client.users.create({
          email: this.registerEmail,
          password: this.registerPassword,
          // this can be added as password confirmation
          passwordConfirm: this.registerPassword
        });

        console.log(user)
        if (user) {
          this.sendmail()
        }
      },

      async sendmail() {
        await client.users.requestVerification(this.email);
      },

      async login () {
        const authData = await client.users.authViaEmail(this.email, this.password);
        if(authData) {
            console.log("Logged In")
            this.status = true;
        }
      },

      logout() {
        client.authStore.clear();
        this.status = false
      }

    }
  }
</script>

<template>
    <div>
        <h1>Register</h1>
        email: <input type="text" v-model="registerEmail">
        <br/>
        password: <input type="password" v-model="registerPassword">
        <br/>
        <button @click="register()">Register</button>

        <hr/>

        <h1>Login</h1>
        email: <input type="text" v-model="email">
        <br/>
        password: <input type="password" v-model="password">
        <br/>
        <button @click="login()">Login</button>
        
        <hr/>
        
        <button @click="logout()">Logout</button>

        <p v-if="this.status">Welcome {{ this.email }}</p>
        <p v-else>You are not signed in!</p>


    </div>
</template>
