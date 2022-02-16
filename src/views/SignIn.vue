<template>
  <div>
    Sign in page! <br />
    <button @click="signIn">Sign in with Google</button>
  </div>
</template>

<script>
import {GoogleAuthProvider, signInWithPopup} from 'firebase/auth'
import { firebaseAuth } from '../firebase'

export default {
  methods: {
    signIn() {
        const provider = new GoogleAuthProvider()

        return signInWithPopup(firebaseAuth, provider)
            .then(() => this.$router.replace({ name: 'Account' }))
    }
  }
}
</script>