<template>
  <div>
    <!-- Demo code ini hanya memunculkan google sign-in button -->
    <h2>VUE 3 Google Login Demo</h2>
    <div v-show="!isLoggedIn" id="google-login-button"></div>

    <div v-if="isLoggedIn">
      <p>Welcome! {{ email }}</p>
      <button @click="googleSignOut">Sign Out</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      email: '',
      isLoggedIn: false,
    }
  },
  methods: {
    setLocalStorage(data) {
      localStorage.access_token = data.accessToken
      localStorage.email = data.email
    },

    googleSignOut() {
      this.resetState()
      localStorage.clear()
    },
    handleCredentialResponse(response) {
      const { credential } = response
      axios({
        method: 'POST',
        url: `http://localhost:3000/google-login`,
        data: {
          credential,
        },
      })
        .then(({ data }) => {
          this.setLocalStorage(data)
          this.email = data.email
          this.isLoggedIn = true
        })
        .catch((err) => {
          console.error('error', 'Oops Login with Google failed...', err)
        })
    },

    googleSignInOnLoad() {
      const cb = this.handleCredentialResponse

      window.onload = function () {
        google.accounts.id.initialize({
          client_id: '225290592554-sk885pi38frutehh9g561mtdhpvg52mf.apps.googleusercontent.com',
          callback: cb,
        })
        google.accounts.id.renderButton(
          document.getElementById('google-login-button'),
          { theme: 'outline', size: 'large' } // customization attributes
        )
      }
    },

    resetState() {
      this.email = ''
      this.isLoggedIn = false
    },
  },

  mounted() {
    if (localStorage.email && localStorage.access_token) {
      this.email = localStorage.email
      this.isLoggedIn = true
    }

    this.googleSignInOnLoad()
  },
}
</script>
