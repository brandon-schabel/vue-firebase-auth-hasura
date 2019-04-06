<template>
  <q-btn @click="signOut">Sign Out</q-btn>
</template>

<script>
import { auth } from '../firebase'
import { mapState } from 'vuex';

export default {
  name: 'SignOutButton',
  computed: {
    ...mapState()
  },
  methods: {
    signOut: async function() {
      try {
        this.setStatus('loading')
        this.setToken(null)
        await auth.signOut().then(() => {
          this.setStatus('out')
          this.$router.push('sign-in')
        })
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>
