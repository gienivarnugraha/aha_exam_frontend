<template>
  <v-card flat class="pa-8">
    <v-card-text>
      <p class="text-h4 text-center font-weight-thin">User Statistics</p>

      <v-row>
        <v-col cols="4">
          <v-card color="primary" :loading="loading" dark>
            <v-card-title class="text-center font-weight-bold text-h4">
              <v-progress-circular
                v-if="loading"
                indeterminate
                color="white"
              ></v-progress-circular>
              <span v-else>
                {{ activeToday }}
              </span>
            </v-card-title>
            <v-card-text>Active Users Today</v-card-text>
          </v-card>
        </v-col>
        <v-col cols="4">
          <v-card color="secondary" :loading="loading" dark>
            <v-card-title class="text-center font-weight-bold text-h4">
              <v-progress-circular
                v-if="loading"
                indeterminate
                color="white"
              ></v-progress-circular>
              <span v-else>
                {{ totalUsers }}
              </span>
            </v-card-title>
            <v-card-text>Total Users</v-card-text>
          </v-card>
        </v-col>
        <v-col cols="4">
          <v-card color="info" :loading="loading" dark>
            <v-card-title class="text-center font-weight-bold text-h4">
              <v-progress-circular
                v-if="loading"
                indeterminate
                color="white"
              ></v-progress-circular>
              <span v-else>
                {{ signedUpAverageLastWeek }}
              </span>
            </v-card-title>
            <v-card-text>Average new users on last week</v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-card-text>

    <v-divider class="my-4"></v-divider>

    <v-card-text>
      <p class="text-h4 text-center font-weight-thin">List of Users</p>
      <v-data-table
        :headers="headers"
        :items="items"
        class="elevation-1"
        item-key="id"
        :loading="loading"
        dark
      >
        <template v-slot:item.createdAt="props">
          {{ $moment.utc(props.item.createdAt).format('MM/DD/YY hh:mm:ss') }}
        </template>
        <template v-slot:item.lastSession="props">
          {{ $moment.utc(props.item.lastSession).format('MM/DD/YY hh:mm:ss') }}
        </template>
      </v-data-table>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  async fetch() {
    this.loading = true
    const { users, average, activeToday } = await this.$axios.$get('/users')
    this.items = users.rows
    this.totalUsers = users.count
    this.activeToday = activeToday
    this.signedUpAverageLastWeek = average.value

    this.loading = false
  },
  data: () => ({
    loading: false,
    totalUsers: 0,
    activeToday: 0,
    signedUpAverageLastWeek: 0,
    headers: [
      { text: 'Name', value: 'name' },
      { text: 'Email', value: 'email' },
      { text: 'Last Login', value: 'lastSession' },
      {
        text: 'Login Times',
        value: 'loginTimes',
        width: '10%',
        align: 'center',
        sortable: false,
      },
      { text: 'Signup at', value: 'createdAt' },
    ],
    items: [],
  }),
}
</script>

