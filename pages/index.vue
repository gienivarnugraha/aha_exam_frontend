<template>
  <div>
    <v-card class="px-8">
      <v-card-title data-cy="welcome">Welcome to dashboard!</v-card-title>
      <v-alert type="success" v-if="success">
        {{ successMsg }}
      </v-alert>
      <v-list three-line class="my-4">
        <v-list-item>
          <v-list-item-avatar size="56">
            <img :src="`${$auth.user.avatar}`" :alt="`${$auth.user.name}`" />
          </v-list-item-avatar>
          <v-list-item-content>
            <v-list-item-title class="d-inline-flex" data-cy="verified-name">
              <v-fade-transition>
                <v-text-field
                  v-if="edit"
                  filled
                  style="max-width: 80%"
                  label="User Name"
                  color="primary"
                  v-model="name"
                ></v-text-field>
                <span v-else>
                  {{ name }}
                </span>
              </v-fade-transition>
              <v-btn
                small
                class="ml-4"
                color="secondary"
                @click="changeName"
                dark
                ><v-icon left>{{ edit ? 'mdi-check' : 'mdi-pencil' }}</v-icon>
                {{ edit ? 'Done' : 'Edit' }}
              </v-btn>
            </v-list-item-title>
            <v-list-item-subtitle data-cy="verified-email"
              >{{ $auth.user.email }}
            </v-list-item-subtitle>
          </v-list-item-content>
          <v-list-item-action>
            <v-btn color="error" @click="logout" small>Logout</v-btn>
          </v-list-item-action>
        </v-list-item>
      </v-list>
    </v-card>
    <v-tabs grow dark v-model="tab">
      <v-tab data-cy="dashboard"> Dashboard </v-tab>
      <v-tab data-cy="reset-password"> Reset Password </v-tab>
    </v-tabs>
    <v-tabs-items v-model="tab">
      <v-tab-item>
        <Dashboard />
      </v-tab-item>

      <v-tab-item>
        <Reset />
      </v-tab-item>
    </v-tabs-items>
  </div>
</template>

<script>
//import { googleLink } from '../helpers/google.js'
export default {
  middleware: 'auth',
  layout: 'default',
  data: () => ({
    tab: 0,
    edit: false,
    name: '',
    success: false,
    successMsg: '',
  }),
  mounted() {
    this.name = this.$auth.user.name
    // console.log(googleLink)
  },
  methods: {
    changeName() {
      this.edit = !this.edit

      if (!this.edit) {
        this.$axios
          .put('/user', { name: this.name })
          .then((res) => {
            this.success = true
            this.successMsg = res.data.message
            setTimeout(() => {
              this.edit = false
              this.success = false
            }, 5000)
          })
          .catch((err) => {
            console.log(err)
          })
      }
    },
    logout() {
      this.$axios
        .get('/logout')
        .then((res) => {
          console.log(res)
          this.$auth.logout()
        })
        .catch((err) => {
          console.log(err)
        })
    },
  },
}
</script>
