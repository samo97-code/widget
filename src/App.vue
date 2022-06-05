<template>
  <div :id="containerId">
    <slot name="test"></slot>
    <div class="widget-button" @click="openWidget">
      <svg width="100%" height="100%" viewBox="0 0 30 33" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path fill-rule="evenodd" clip-rule="evenodd"
              d="M21.333 26.324l6.449 6.376a.752.752 0 001.28-.535V21.44l-7.729 4.884z" fill="#ffffff"></path>
        <path
            d="M3.946 0h22.109a3.008 3.008 0 013.008 3.008V21.44l-7.624 5.004H3.945a3.008 3.008 0 01-3.007-3.008V3.008A3.008 3.008 0 013.946 0z"
            fill="#ffffff"></path>
        <path fill-rule="evenodd" clip-rule="evenodd" d="M21.439 26.444L.937 4.981v21.463H21.44z"
              fill="url(#gradient)"></path>
        <defs>
          <linearGradient id="gradient" x1="13.451" y1="12.325" x2="5.559" y2="22.297" gradientUnits="userSpaceOnUse">
            <stop stop-color="#000000" stop-opacity=".2"></stop>
            <stop offset="1" stop-color="transparent" stop-opacity="0"></stop>
          </linearGradient>
        </defs>
      </svg>
    </div>

    <div :class="{'opened':show}" class="widget-form">
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
            <!--            <myCaptcha ref="captcha" :callSuccess="captchaBtn" color="#72BF44FF" resolve="digit"></myCaptcha>-->
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
      objectProps: {}
    }
  },
  computed: {
    canSend() {
      return this.form.check && this.form.email && this.form.message
    },
    containerId(){
      if (this.objectProps.container){
        if (this.objectProps.container.charAt(0) === '#'){
          return this.objectProps.container.substring(1)
        }

        return this.objectProps.container
      }

      return 'contact'
    }
  },
  mounted () {
    this.objectProps = document.querySelector('vue-widget').objectProp
  },
  methods: {
    openWidget() {
      this.show = true
    },
    close() {
      this.show = false
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
.vue-captcha {
  border: none !important;
}
.content-ok{
  width: 160px !important;
}
</style>
