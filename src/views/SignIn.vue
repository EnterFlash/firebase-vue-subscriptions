<template>
  <default-layout>
    Sign in page! <br />
    <button @click="signIn">Sign in with Google</button>
  </default-layout>
</template>

<script>
import DefaultLayout from "@/layouts/DefaultLayout";
import {GoogleAuthProvider, signInWithPopup} from 'firebase/auth'
import { firebaseAuth } from '../firebase'

export default {
  components: { DefaultLayout },
  methods: {
    signIn() {
        const provider = new GoogleAuthProvider()

        return signInWithPopup(firebaseAuth, provider)
            .then(() => this.$router.replace({ name: 'Account' }))
    }
  }
}
</script>