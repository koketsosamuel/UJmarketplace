<template>
  <div class="p-2 yellow lighten-2">

    <div class="collection mt-3" v-if="notifications.length > 0">
      <a href="javascript:void(0)" v-for="notification in notifications" :key="notification._id" class="collection-item black-text" @click="setEditData(notification)" >
        <div> 
          <p class="left">{{notification.title}} </p>

          <span class="right">
            <div>
              <a href="#notificationEdit" class="btn modal-trigger blue"><i class="fa fa-pen"></i></a>
              <a href="#notificationImage" class="btn modal-trigger brown"><i class="fa fa-image"></i></a>
              <a href="#" class="btn red" @click="remove(notification._id)"><i class="fa fa-trash"></i></a>
            </div>
          </span>

          <div class="clearfix"></div>

        </div>
      </a>
    </div>


    <!-- NOTIFICATIONS EDIT MODAL -->
    <div id="notificationEdit" class="modal teal">
      <div class="modal-content">
        <div>
          <h5 class="white-text">Edit notification</h5>
          <p>Name</p>
          <input type="text" placeholder="Name" v-model="notification.title">
          <p>Link</p>
          <input type="text" placeholder="Link" v-model="notification.link">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="update()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

    <!-- NOTIFICATIONS IMAGE UPDATE -->
    <div id="notificationImage" class="modal teal">
      <div class="modal-content">
        <div>
          
          <h5 class="white-text">Update Notification Image | ratio has to be 16:6</h5>

          <div class="notImg">
            <img :src="this.$baseUrl+notification.image" alt="">
          </div>
 
          <input type="file" name="" ref="notImg">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="updateImage()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>


    <div v-if="notifications.length == 0" class="purple p-2 mt-2">
      <p class="display-1 center  p-2 mt-2">
        <i class="fa fa-times-circle fa-4x" aria-hidden="true"></i>
      </p>
      <p class="center">No Notifications</p>

    </div>

    

  </div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex'


export default {

  name: "adminCampuses",
  data() {
    return {
      notification: {
        title: '',
        link: '',
        image: '',
        _id: ''
      }
    }
  },
  methods: {
    ...mapActions(['getNotifications']),

    setEditData(not) {
      this.notification = not
    },

    update() {
      
      let loader = this.$loading.show()

      this.$axios.put(`/notification/update/${this.notification._id}`, this.notification)
        .then(res => {
          
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.getNotifications()
          }

          loader.hide()          

        }).catch(err => {

          this.$toasted.error(err, {icon:"times-circle"})
          loader.hide()

        })

    },

    remove(id) {

      if(confirm("Are you sure you want to remove this notification?")) {
        let loader = this.$loading.show()

        this.$axios.delete(`/notification/remove/${id}`, this.$headers())
          .then(res => {
            
            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon: "times-circle"})
            } else {
              
              this.$toasted.success(res.data.message, {icon: "check"})
              this.notification.title = ''
              this.notification.shortDesc = ''
              this.notification.notification = ''
              this.notification.id = ''
              this.getNotifications()

            }

            loader.hide()

          })
            .catch(err => {
              
              this.$toasted.error(err, {icon: "times-circle"})
              loader.hide()

            })
      }
    },

    update() {
      
      let loader = this.$loading.show()

      this.$axios.put(`/notification/update/${this.notification._id}`, this.notification, this.$headers())
        .then(res => {
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})           
            this.getNotifications()
          }

          loader.hide()

        }).catch(err => {
          loader.hide()
          this.$toasted.error(err, {icon: "times-circle"})
        })

    },

    updateImage() {
      
      let loader = this.$loading.show()

      let fd = new FormData()

      fd.append("notificationImg", this.$refs.notImg.files[0], this.$refs.notImg.files[0].name)

      this.$axios.put(`/notification/update/image/${this.notification._id}`, fd, this.$headers())
        .then(res => {
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.getNotifications()
          }

          loader.hide()

        }).catch(err => {
          loader.hide()
          this.$toasted.error(err, {icon: "times-circle"})
        })

    }

  },
  computed: mapGetters(['notifications']),
  created() {
    this.getNotifications()
  }


}
</script>

<style scoped>
  .notImg {
    max-height: 400px;
    justify-content: center;
    padding-top: 10px;
  }

  .notImg img {
    max-height: 200px;
    max-width: 100%;
  }
</style>
