<template>
  <form @submit.prevent="submitForm">
    <div class="row q-mb-md">
      <q-banner class="col bg-grey-3">
        <template v-slot:avatar>
          <q-icon name="account_circle" color="primary" />
        </template>
        {{ tab | titleCase }} to use Task Manager!
      </q-banner>
    </div>

    <div class="row q-mb-md">
      <q-input
        v-model="formData.email"
        :rules="[ val => isValidEmailAddress(val) || 'Please provide a valid e-mail address']"
        ref="email"
        type="email"
        lazy-rules
        autofocus
        outlined
        class="col"
        :label="tab"
        stack-label />
    </div>
    <div class="row q-mb-md">
      <q-input
        v-model="formData.password"
        :rules="[ val => val.length >= 6 || 'Please use at least 6 characters']"
        ref="password"
        type="password"
        lazy-rules
        outlined
        class="col"
        :label="tab"
        stack-label />
    </div>
    <div class="row">
      <q-space />
      <q-btn
        color="primary"
        :label="tab"
        type="submit" />
    </div>
  </form>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: ['tab'],
  data() {
    return {
      formData: {
        email: '',
        password: ''
      }
    }
  },
  methods: {
    ...mapActions('auth', ['registerUser', 'loginUser']),

    isValidEmailAddress(email) {
      let rg = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      return rg.test(String(email).toLowerCase())
    },

    submitForm() {
      this.$refs.email.validate()
      this.$refs.password.validate()
      if (!this.$refs.email.hasError && !this.$refs.password.hasError) {
        if (this.tab === 'login') {
          this.loginUser(this.formData)
        } else {
          this.registerUser(this.formData)
        }
      }
    }
  },
  filters: {
    titleCase(value) {
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
}
</script>

<style>

</style>
