<template>
  <v-card flat class="d-flex justify-center">
    <v-btn color="secondary" @click="loginGoogle" data-cy="login-google">
      <v-icon>mdi-google</v-icon>
    </v-btn>
    <v-divider vertical class="mx-4"></v-divider>
    <v-btn color="primary" @click="loginFacebook">
      <v-icon>mdi-facebook</v-icon>
    </v-btn>
  </v-card>
</template>

<script>
export default {
  methods: {
    loginGoogle() {
      this.$auth
        .loginWith('google', {
          params: { prompt: 'consent', access_type: 'offline' },
        })
        .catch((err) => {
          console.log(err)
        })
    },
    async loginFacebook() {
      this.$auth.loginWith('facebook').catch((err) => {
        console.log(err)
      })
    },
  },
}
</script>

