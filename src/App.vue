<template>
  <div :id="containerId">
    <div id="widget-form" class="widget-form">
      <div class="widget-header">
        <span class="title">{{ objectProps.text ? objectProps.text : 'Contact Us'}}</span>

        <svg class="close-icon" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" @click="close">
          <path
              d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"
              fill="#fff"></path>
        </svg>
      </div>

      <div class="widget-body">
        <div class="form">
          <div class="form-group">
            <label for="name">Name</label>
            <input id="name" type="text" placeholder="Name" v-model="form.name">
          </div>

          <div class="form-group">
            <label for="email">Email*</label>
            <input id="email" type="email" placeholder="Email*" v-model="form.email">
          </div>

          <div class="form-group">
            <label>Message*</label>
            <textarea id="message" v-model="form.message" placeholder="Message*"></textarea>
          </div>

          <div class="form-group form-captcha">
            <input id="checkbox" type="checkbox" v-model="form.check">
            <label for="checkbox">I’m not a robot*</label>
          </div>

          <div class="text-center">
            <button type="button" class="send-btn" @click="send" :disabled="!canSend" :class="{'disabled':!canSend}">
              Send
            </button>
          </div>
        </div>
      </div>

    </div>

    <Snackbar :snackbar="snackbar" @close-snackbar="closeSnackbar"/>
  </div>
</template>

<script>
import axios from 'axios'
import Snackbar from "./components/Snackbar";
export default {
  name: 'App',
  components: {
    Snackbar
  },
  data() {
    return {
      snackbar: {
        show: false,
        mode: 'success',
        message: ''
      },
      form:{
        name: '',
        email: '',
        message: '',
        check: false
      },
      show: false,
      objectProps: {
        text: "Contact us",
        container: "#contact",
        endpoint: 'https://services.trackingmax.com/contact-form-receive',
        thankYouContent: 'Thank you! We will contact you in next 24h'
      }
    }
  },
  computed: {
    canSend() {
      return this.form.check && this.form.email && this.form.message
    },
    containerId(){
      if (!this.objectProps.container) return 'contact'

      if (this.objectProps.container.charAt(0) === '#'){
        return this.objectProps.container.substring(1)
      }

      return this.objectProps.container
    }
  },
  mounted () {
    if (document.querySelector('vue-widget').objectProp){
      this.objectProps = document.querySelector('vue-widget').objectProp
    }
  },
  methods: {
    close() {
      const widget = document.getElementById('widget-form')
      widget.classList.remove('opened')
    },
    closeSnackbar() {
      this.snackbar.show = false
    },
    setSnackbar(data){
      this.snackbar = {
        show: true,
        mode: data.success ? 'success' : 'error',
        message: data.success && this.objectProps?.thankYouContent ? this.objectProps.thankYouContent : data.message
      }
    },
    resetValues(){
      this.form.name = ''
      this.form.email = ''
      this.form.message = ''
      this.form.check = false
    },
    async send() {
      if (this.canSend){
        const data = {
          name: this.form.name,
          email: this.form.email,
          message: this.form.message,
        }

        try {
          const resp = await axios.post(this.objectProps.endpoint, data)
          await this.setSnackbar(resp.data)
          if (resp.data.success){
            await this.resetValues()
            await this.close()
          }

        } catch (e) {
          this.snackbar = {
            show: true,
            mode: 'error',
            message: e.message
          }
        }
      }
    }
  }
}
</script>

<style scoped>
@import "assets/style.css";
</style>

<style>
#contact {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

body {
  margin: 0;
  padding: 0;
}
</style>
