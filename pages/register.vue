<template>
  <v-row justify="center">
    <v-col cols="6" align-self="center">
      <v-card class="pa-8">
        <v-card-title primary-title> Register AHA Backend Exam </v-card-title>
        <v-alert
          type="error"
          v-if="error.type === 'register'"
          data-cy="register-error"
        >
          {{ error.message }}
        </v-alert>
        <v-alert type="success" v-if="success" data-cy="register-success">
          {{ successMsg }}
        </v-alert>
        <v-card-text>
          <v-form v-model="valid">
            <v-text-field
              name="name"
              color="secondary"
              label="Name"
              type="Name"
              data-cy="name"
              filled
              :error="error.name"
              :error-messages="errorMsg.name"
              v-model="form.name"
            ></v-text-field>
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
              color="secondary"
              label="Password"
              data-cy="password"
              :type="showPassword ? 'text' : 'password'"
              append-icon="mdi-eye"
              filled
              @click:append="showPassword = !showPassword"
              :error="error.password"
              :error-messages="errorMsg.password"
              v-model="form.password"
            ></v-text-field>
            <v-text-field
              color="secondary"
              label="Password Confirmation"
              data-cy="password-confirm"
              :type="showPassword ? 'text' : 'password'"
              append-icon="mdi-eye"
              filled
              @click:append="showPassword = !showPassword"
              :error="error.passwordConfirmation"
              :error-messages="errorMsg.passwordConfirmation"
              v-model="form.passwordConfirmation"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions class="justify-center">
          <v-row>
            <v-col cols="6">
              <v-btn
                color="primary"
                :loading="loading"
                block
                text
                nuxt
                to="/login"
              >
                <v-icon left>mdi-arrow-left</v-icon> Back to Login
              </v-btn>
            </v-col>
            <v-col cols="6">
              <v-btn
                color="primary"
                :loading="loading"
                data-cy="submit"
                block
                @click="register"
              >
                <v-icon left>mdi-lock</v-icon> Register
              </v-btn>
            </v-col>
          </v-row>
        </v-card-actions>
        <p class="text-center">or</p>
        <Social />
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
//import { googleLink } from '../helpers/google.js'
export default {
  layout: 'login',
  data: () => ({
    form: {
      name: '',
      email: '',
      password: '',
      passwordConfirmation: '',
    },
    valid: true,
    error: {},
    showPassword: false,
    errorMsg: {},
    successMsg: '',
    success: false,
    loading: false,
  }),
  methods: {
    register() {
      this.loading = true
      this.$axios
        .post('/register', this.form)
        .then((res) => {
          this.success = true
          this.successMsg = res.data.message

          for (const key in this.form) {
            this.form[key] = ''
          }
        })
        .catch((err) => {
          console.log(err)
          if (err.response?.data.type === 'validation') {
            for (const key in err.response.data.message.errors) {
              this.error[key] = true
            }
            this.$nextTick(() => {
              this.errorMsg = err.response.data.message.errors
            })
          } else {
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
  },
}
</script>
