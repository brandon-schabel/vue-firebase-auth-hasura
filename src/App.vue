<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import {auth, db} from './firebase'
import { mapState } from 'vuex'


export default {
  name: 'app',
  components: {
    HelloWorld
  },
  methods: {
  },
  computed: {
    ...mapState()
  },
  created() {
    auth.onAuthStateChanged(async user => {
      if (user) {
        const token = await user.getIdToken()
        const idTokenResult = await user.getIdTokenResult()
        const hasuraClaim = idTokenResult.claims['https://hasura.io/jwt/claims']

        if (hasuraClaim) {
          this.setToken(token)
          this.setStatus('in')
        } else {
          const metadataRef = db.collection('metadata').doc(user.uid)

          metadataRef
            .get()
            .then(async doc => {
              if (doc.exists && doc.data().refreshTime) {
                const token = await user.getIdToken(true)
                this.setToken(token)
                this.setStatus('in')
              } else {
                // console.log('Refresh time doesnt exist')
                this.setToken(null)
                this.setStatus('out')
              }
            })
            .catch(error => {
              console.log('Error getting refresh time ', error)
            })
        }
      }
    })
  }
}
</script>

<style>

</style>
