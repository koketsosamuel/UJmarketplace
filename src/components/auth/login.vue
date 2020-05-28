<template>
  
  <div>

  <div class="container purple p-2 mt-2 mb-4">

    <h5 class="white-text">
      <i class="fa fa-user d-inline"></i> Login
    </h5>

    <!-- STUDENT NO -->
    <div class="form-group">
      <label for="sNo" class="white-text"><i class="fa fa-graduation-cap" aria-hidden="true"></i> Student No.</label>
      <input v-model="sNo" type="text" id="sNo" class="form-control" placeholder="e.g 212002355" aria-describedby="helpName">
    </div>


    <div class="form-group">
      <label for="pwd" class="white-text"><i class="fa fa-lock" aria-hidden="true"></i> Password</label>
      <input v-model="pwd" type="password" id="pwd" class="form-control">
    </div>

    <button class="btn orange btn-lg" @click="login">Login</button>

    <div class="alert mt-2 border-primary" role="alert">
      <router-link to="/register"><a href="#" class="btn blue btn-sm text-white">Register</a></router-link> | <a class="modal-trigger white-text" href="#pwdReset">Forgot password?</a>
    </div>

  </div>


  <!-- MODAL -->
  <div class="modal orange darken-4" id="pwdReset">
 
      <div class="modal-content">
        <!-- STUDENT NO -->
        <div class="form-group">
          <label for="sNoMod" class="white-text"><i class="fa fa-graduation-cap" aria-hidden="true"></i> Student No.</label>
          <input v-model="sNoReset" type="text" id="sNoMod" class="form-control" placeholder="e.g 212002355" aria-describedby="helpName">

          <div class="text-dark font-italic text-muted">
            <p>You will receive an email with a link, and the link will expire in 10 minutes</p>
          </div>

        </div>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn modal-close orange darken-4" data-dismiss="modal" @click="resetLink">Submit</button>
        <button type="button" class="btn modal-close white black-text" data-dismiss="modal">Cancel</button>
      </div>
    </div>
  </div>


</template>

<script>

// import modules
import {mapActions} from 'vuex'
import jsCookie from 'js-cookie'

export default {

  name: "login",
  data(){

    return {
      sNo: '',
      pwd: '',
      sNoReset: ''
    }

  },


  methods: {

    ...mapActions(['authenticate']),

    // login method
    login(){

      this.$axios.post('/user/login', {studentNo: this.sNo, password: this.pwd})
        .then(res => {

          // if error 
          if(res.data.error) {
            this.$toasted.error(res.data.message,{icon: "times-circle"})
          }  else {

            // cookie setting
            jsCookie.set('4u7h3n71c4710n', {token: res.data.token, authData: res.data.authData}, {expires: 9000})

            // auth 
            this.authenticate(res.data.authData)

            this.$toasted.success("Logged In", {icon: "check-circle"})

            // redirection
            this.$router.push('/')


          }
          

        }).catch(err => {
          // toast
          // console.log(err)
          this.$toasted.error(err,{icon: "times-circle"})
        })

        

    },

    resetLink() {

      if(this.sNoReset.length > 0) {

        let loader = this.$loading.show()

        this.$axios.post("/user/resetlink", {sNo: this.sNoReset})
        .then(res => {

          loader.hide()

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon:"times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon:"check"})
          }

        }).catch(err => {
          loader.hide()          
          this.$toasted.error(err, {icon:"times-circle"})
        })
      } else {
        this.$toasted.error("Enter Student number / number on your UJ access card", {icon:"times-circle"})
      }

    }

  }

}
</script>

<style scoped>
  .form {
    margin-bottom: 60px;
  }
</style>
