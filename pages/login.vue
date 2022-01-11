<template>
  <div>
    <Loader style="position: absolute" v-if="$auth.$state.busy" />
    <v-row justify="center">
      <v-col cols="6" align-self="center">
        <v-card
          class="px-8 pb-8"
          :loading="loadingToken"
          :disabled="loadingToken"
        >
          <v-card-title class="pt-8"> Login AHA Backend Exam </v-card-title>
          <v-alert
            type="error"
            v-if="error.type === 'login'"
            data-cy="login-error"
          >
            {{ error.message }}
          </v-alert>
          <v-alert type="success" v-if="success" data-cy="register-success">
            {{ successMsg }}
          </v-alert>
          <v-alert type="error" v-if="notVerified" data-cy="verification-error">
            <v-row align="center">
              <v-col class="grow">
                You are not verified yet! Didn't receive the token?
              </v-col>
              <v-col class="shrink">
                <v-btn @click="resendToken" :loading="loadingResend"
                  >Resend Token</v-btn
                >
              </v-col>
            </v-row>
          </v-alert>
          <v-card-text>
            <v-form v-model="valid">
              <v-text-field
                name="email"
                color="secondary"
                label="Email"
                type="Email"
                data-cy="email"
                filled
                :error="error.email"
                :error-messages="errorMsg.email"
                v-model="form.email"
              ></v-text-field>
              <v-text-field
                name="password"
                color="secondary"
                label="Password"
                data-cy="password"
                filled
                :type="showPassword ? 'text' : 'password'"
                :append-icon="showPassword ? 'mdi-eye-off' : 'mdi-eye'"
                @click:append="showPassword = !showPassword"
                :error="error.password"
                :error-messages="errorMsg.password"
                v-model="form.password"
              ></v-text-field>
            </v-form>
            <v-btn
              color="primary"
              :loading="loading"
              data-cy="submit"
              @click="loginBasic"
              block
            >
              <v-icon left>mdi-lock</v-icon> Login
            </v-btn>
            <div class="my-4"></div>
            <v-btn
              color="primary"
              :loading="loading"
              data-cy="register"
              block
              text
              nuxt
              to="/register"
            >
              <v-icon left>mdi-lock</v-icon> Register
            </v-btn>
          </v-card-text>
          <p class="text-center">or</p>
          <Social />
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
//import { googleLink } from '../helpers/google.js'
export default {
  layout: 'login',
  data: () => ({
    form: {
      email: '',
      password: '',
    },
    valid: true,
    error: {},
    errorMsg: {},
    success: false,
    successMsg: '',
    showPassword: false,
    notVerified: false,
    loading: false,
    loadingToken: false,
    loadingResend: false,
  }),
  mounted() {
    const token = this.$route.query.token

    if (token) {
      this.loadingToken = true
      this.$axios
        .post('/token_verification', { token })
        .then((res) => {
          let { accesstoken } = res.data

          this.$auth.setUserToken(accesstoken)

          setTimeout(() => {
            this.loadingToken = false
            this.$router.push('/')
          }, 1000)
        })
        .catch((err) => {
          this.loadingToken = false
          console.log(err)
          this.error = err.response.data
        })
    }
  },
  methods: {
    loginBasic() {
      this.loading = true
      this.$auth
        .loginWith('local', { data: this.form })
        .then((res) => {
          console.log(res)
          this.$auth.setUser(res.data.user)
          setTimeout(() => {
            this.$router.push({ path: '/' })
          }, 1000)
        })
        .catch((err) => {
          if (err.response?.data.type === 'validation') {
            for (const key in err.response.data.message.errors) {
              this.error[key] = true
            }
            this.$nextTick(() => {
              this.errorMsg = err.response.data.message.errors
            })
          } else if (err.response?.data.type === 'verification') {
            this.notVerified = true
          } else {
            console.log(err)
            this.error = err.response.data
          }
        })
        .finally(() => {
          setTimeout(() => {
            this.error = {}
            this.errorMsg = {}
          }, 5000)
          this.loading = false
        })
    },
    resendToken() {
      this.loadingResend = true
      this.$axios
        .post('/resend_token', { email: this.form.email })
        .then((res) => {
          console.log(res)
          this.notVerified = false
          this.success = true
          this.successMsg = res.data.message
        })
        .catch((err) => {
          console.log(err)
        })
        .finally(() => {
          this.loadingResend = false
        })
    },
  },
}
</script>
