<template>
  <div id="app">

    <navigation />

    <!-- INJECTED COMPONENT  -->
    <div class="main-view">
      <router-view></router-view>
    </div>

    <footer-component />


  </div>
</template>

<script>

// importations
import { mapActions, mapGetters } from 'vuex'
import jsCookies from 'js-cookie'
import Vue from "vue"

export default {
  name: 'app',

  methods: {
    ...mapActions(['authenticate']),

    auth() {
      let r = this.$route.name
      let protectedRoutes = ["admin","addPost","editPost","managePictures","profile","myAds", "searchUser"]
      let adminRoutes = ["admin", "searchUser"]

      if(!this.authInfo.loggedIn) {

        if(protectedRoutes.includes(r)) {
          this.$toasted.error("Login first", {icon: "times-circle"})
          this.$router.replace("/login")
        }

      } else {

        if(this.authInfo.position != 'admin' && adminRoutes.includes(r)) {
          this.$toasted.error("Not Authorized", {icon: "times-circle"})
          this.$router.replace("/")
        }

      }
    }
  },

  computed: mapGetters(['authInfo']),

  async created() {

    if(jsCookies.getJSON('4u7h3n71c4710n') != undefined){
      await this.authenticate(jsCookies.getJSON('4u7h3n71c4710n').authData)
    }


    await this.auth()
    Vue.nextTick(() => {
      this.$M.AutoInit()
    })

  },

  watch: {

    $route() {

      this.auth()
      scroll(0,0)
      Vue.nextTick(() => {
        this.$M.AutoInit()
      })
    }

  }
}
</script>

<style>

  * {
    border-radius: 0px !important;
    box-sizing: border-box !important;
    
  }

  .btn-floating {
    border-radius: 50% !important;
  }

  #app {
    margin: 0px !important;
    padding: 0px !important;
    
    margin-bottom: -20px !important;
  }

  body {
    min-height: 100%;
    padding-top:0px !important;
    margin-top: 0px !important;
  }

  .cursor {
    cursor: pointer;
  }

  .jumboImage {
    height: 200px;
    vertical-align: middle;
  }

  .orange {
    background: #FF4500 !important;
    color: #fff !important;
    transition: all 0.3s;
  }

  .btn.orange:hover {
    background: rgb(212, 57, 0) !important;
  }

  .text-orange {
    color: #FF4500 !important;
  }

  .btn {
    cursor: pointer;
  }


  ::-webkit-scrollbar {
    width: 10px;
    background: #FF4500;
  }

  ::-webkit-scrollbar-thumb {      
    background: #b8b7b7;
  }

  ::-webkit-scrollbar-thumb:hover {
    background: #772953;
  }

  .shadow-black {
    text-shadow: #000 2px 2px 2px;
  }

  .shadow-white {
    text-shadow: rgb(255, 255, 255) 1px 1px 1px;
  }

  
  .flex {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100% !important;
  } 

  .post-add {
    position: fixed !important;
    right: 0;
    bottom: 20px;
    transition: 0.3s all;
    opacity: 0.75;
  }

  .post-add:hover {
    opacity: 1;
  }

  .h-100 {
    height: 100% !important;
  }

  .p-2 {
    padding: 20px!important;
  }

  .p-0 {
    padding: 0px !important;
  }

  input:not(#search), textarea {
    /* border-radius: 5px !important; */
    border: 1px solid #222 !important;
    padding: 5px !important;
  }

  /* .form-select {
    display: block;
    background: transparent;
    border: #000 1px solid;
  } */

  label {
    color: #222;
  }

  .d-none {
    display: none;
  }

  .mt-2 {
    margin-top: 20px;
  }

  .mt-4 {
    margin-top: 40px;
  }

  .mb-2 { 
    margin-bottom: 20px !important;
  }

  .mb-4 {
    margin-bottom: 40px !important;
  }

  .p-1 {
    padding: 10px !important;
  }

  .p-4 {
    padding: 40px !important;
  }

  .card-title {
    background: #772953c7 !important;
    padding: 10px !important;
    font-size: 18px !important;
    width: 100% !important;
  }


  label {
    color: #fff !important;
  }  


</style>
