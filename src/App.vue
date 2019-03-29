<template>
  <div id="q-app" v-if="hydrated">
    <router-view/>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      signedIn: false,
      hydrated: false
    }
  },
  async mounted () {
    await this.$apollo.provider.defaultClient.hydrated()
    this.hydrated = true
  },
  beforeCreate () {
    this.$AmplifyEventBus.$on('authState', info => {
      if (info === 'signedIn') {
        this.signedIn = true
        this.$router.push('/')
      }
      if (info === 'signedOut') {
        this.$router.push('/auth')
        this.signedIn = false
      }
    })

    this.$Auth.currentAuthenticatedUser()
      .then(user => {
        this.signedIn = true
      })
      // eslint-disable-next-line
      .catch(() => this.signedIn = false)
  }
}
</script>

<style>
#q-app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  padding-top: 20px;
  padding-bottom: 20px;
}
</style>
