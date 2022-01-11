<template>
  <v-card class="px-8 py-4">
    <v-card-title v-if="$auth.user.isPasswordDefault">
      Your default password is "Password123!" Please change it immediately
    </v-card-title>
    <v-card-text>
      <v-form>
        <v-alert data-cy="success-message" type="success" v-if="success">
          Success resetting your password!
        </v-alert>
        <v-text-field
          name="oldpassword"
          color="secondary"
          label="Old Password"
          data-cy="old-password"
          :type="show.oldPassword ? 'text' : 'password'"
          append-icon="mdi-eye"
          @click:append="show.oldPassword = !show.oldPassword"
          :error="error.oldPassword"
          :error-messages="errorMsg.oldPassword"
          v-model="form.oldPassword"
        ></v-text-field>
        <v-divider class="my-4"></v-divider>
        <v-text-field
          name="newpassword"
          color="secondary"
          label="New Password "
          data-cy="new-password"
          :type="show.newPassword ? 'text' : 'password'"
          append-icon="mdi-eye"
          @click:append="show.newPassword = !show.newPassword"
          :error="error.newPassword"
          :error-messages="errorMsg.newPassword"
          v-model="form.newPassword"
        ></v-text-field>
        <v-text-field
          name="confirmpassword"
          color="secondary"
          label="Password Confirmation"
          data-cy="confirm-password"
          :type="show.confirmPassword ? 'text' : 'password'"
          append-icon="mdi-eye"
          @click:append="show.confirmPassword = !show.confirmPassword"
          :error="error.confirmPassword"
          :error-messages="errorMsg.confirmPassword"
          v-model="form.confirmPassword"
        ></v-text-field>
      </v-form>
    </v-card-text>
    <v-card-actions>
      <v-btn
        :loading="loading"
        @click="changePassword"
        color="error"
        data-cy="submit"
        >Reset</v-btn
      >
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  data: () => ({
    loading: false,
    success: false,
    form: {
      oldPassword: '',
      newPassword: '',
      confirmPassword: '',
    },
    show: {
      oldPassword: false,
      newPassword: false,
      confirmPassword: false,
    },
    errorMsg: {},
    error: {},
  }),
  methods: {
    changePassword() {
      this.loading = true
      this.$axios
        .put('/reset_password', this.form)
        .then((res) => {
          this.success = true
          for (const key in this.form) {
            this.form[key] = ''
          }
        })
        .catch((err) => {
          console.log(err.response.data)
          if (err.response.data.type === 'validation') {
            for (const key in err.response.data.message.errors) {
              this.error[key] = true
            }
            this.$nextTick(() => {
              this.errorMsg = err.response.data.message.errors
            })
          }
        })
        .finally(() => {
          setTimeout(() => {
            this.error = {}
            this.errorMsg = {}
            this.success = false
          }, 5000)
          this.loading = false
        })
    },
  },
}
</script>
