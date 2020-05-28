<template>
  
  <div>

    <!-- TOOLBAR -->
    <div class="card-panel purple">
      <a class="btn teal modal-trigger" href="#categoryAdd"><i class="fa fa-plus"></i> Category</a>
      <a class="btn green modal-trigger" href="#campusAdd"><i class="fa fa-plus"></i> Campus</a>
      <a class="btn brown modal-trigger" href="#notificationAdd" ><i class="fa fa-plus"></i> Notification</a>
    </div>

    <!-- CATEGORY -->
    <div id="categoryAdd" class="modal teal">
      <div class="modal-content">
        <div>
          <h5 class="white-text">Add Category</h5>
          <p>Name</p>
          <input type="text" placeholder="Name" v-model="category.name">
          <p>Expiration Days</p>
          <input type="text" placeholder="Expiration Days" v-model="category.expirationDays">
          <p>Image</p>
          <input type="file" ref="cat">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="addCategory()">Add</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

    <!-- CAMPUS -->
    <div id="campusAdd" class="modal green">
      <div class="modal-content">
        <div>
          <h5 class="white-text">Add Campus</h5>

          <p>Name</p>
          <input type="text" placeholder="Name" v-model="campus">          
          <p>Image</p>
          <input type="file" src="" alt="" ref="campus">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn green" @click="addCampus()">Add</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

     <!-- NOTIFICATION -->
    <div id="notificationAdd" class="modal brown">
      <div class="modal-content">
        <div>
          <h5 class="white-text">Add Notification</h5>

          <p>Title</p>
          <input type="text" placeholder="title" v-model="notification.title">  
          <p>Link</p>        
          <input type="text" placeholder="link" v-model="notification.link">  
          <p>Image | ratio has to be 16:6</p>
          <input type="file" ref="not">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn brown" @click="addNotification()">Add</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

    
  </div>

</template>

<script>
import {mapActions} from 'vuex'
import jsCookie from "js-cookie"

export default {

  data() {
    return {
      notification: {
        title: '',
        link: ''
      },

      category: {
        name: null,
        expiration: "yes",
        expirationDays: 120
      },

      campus: ''
    }
  },

  methods: {
    ...mapActions(['getCategories', 'getCampuses', 'getNotifications']),

    addCategory() {

      let loader = this.$loading.show()

      const fd = new FormData()
          
      fd.append("categoryImg", this.$refs.cat.files[0], this.$refs.cat.files[0].name)

      Object.keys(this.category).forEach((key) => {
        fd.append(key, this.category[key])
      })


      this.$axios.post("/category/add", fd, this.$headers())
        .then(res => {

          loader.hide()

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon:"times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon:"check"})
          }

          this.getCategories()

        })
        .catch(err => {
          this.$toasted.error(err, {icon:"times-circle"})
          loader.hide()
        })


    },

    addCampus() {
      
      let loader = this.$loading.show()

      let fd = new FormData()

      fd.append("campusImg", this.$refs.campus.files[0], this.$refs.campus.files[0].name)

      fd.append("name", this.campus)

      this.$axios.post('/campus/add', fd, this.$headers())
        .then(res => {

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon:"times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.campus = ""
            this.getCampuses()
          }

          loader.hide()

        })

        .catch(err => {
          this.$toasted.error(err, {icon:"times-circle"})
          loader.hide()
        })
    },

    addNotification() {

      let loader = this.$loading.show()

      let fd = new FormData()

      fd.append("notificationImg", this.$refs.not.files[0], this.$refs.not.files[0].name)

      Object.keys(this.notification).forEach(key => {
        fd.append(key, this.notification[key])
      })

      this.$axios.post('/notification/add', fd, this.$headers())
        .then(res => {

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon:"times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.notification.title = ""
            this.notification.link = ""
            this.getNotifications()
          }

          loader.hide()

        })

        .catch(err => {
          this.$toasted.error(err, {icon:"times-circle"})
          loader.hide()
        })
    }



  }


}
</script>

<style>

</style>
