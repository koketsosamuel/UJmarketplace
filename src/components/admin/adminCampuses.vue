<template>
  <div class="p-2 teal lighten-4">

    <!-- campuses LIST -->
    <div class="collection" v-if="campuses.length > 0">
     
        <div class="collection-item" v-for="campus in campuses" :key="campus._id" @click="selected(campus)"> 

          <span class="left">{{campus.name}}</span>

          <button class="btn red right" @click="remove(campus._id)"><i class="fa fa-trash" aria-hidden="true"></i> </button>
          <a href="#campusImage" class="btn teal right small modal-trigger"><i class="fa fa-image" aria-hidden="true"></i> </a>
          <a href="#campusEdit" class="btn green right modal-trigger" ><i class="fa fa-pen" aria-hidden="true"></i> </a>
          <div class="clearfix"></div>

        </div>
    </div>

    <!-- campus EDIT MODAL -->
    <div id="campusEdit" class="modal teal">
      <div class="modal-content">
        <div>

          <h5 class="white-text">Edit campus</h5>
          <p>Name</p>
          <input type="text" placeholder="Name" v-model="campus.name">          

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="update()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

    <!-- campus IMAGE UPDATE -->
    <div id="campusImage" class="modal teal">
      <div class="modal-content">
        <div>
          
          <h5 class="white-text">Update campus Image</h5>

          <div class="catImg">
            <img :src="this.$baseUrl+campus.image" alt="">
          </div>
 
          <input type="file" name="" ref="campusImg">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="updateImage()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>


    <div v-if="campuses.length == 0" class="purple mt-2 p-2 center">
      <p class="display-1 text-center">
        <i class="fa fa-times-circle fa-4x" aria-hidden="true"></i>
      </p>
      <p class="text-center">No campuses</p>
    </div>

    

  </div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex'


export default {

  name: "admincampuses",
  data() {
    return {
      campus: {
        name: '',
        _id: '',
        expiration: null,
        expirationDays: null,
        image: ""
      }
    }
  },
  methods: {
    ...mapActions(['getCampuses']),

    update() {
      
      let loader = this.$loading.show()

      this.$axios.put(`/campus/update/${this.campus._id}`, this.campus, this.$headers())
        .then(res => {
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.campus.name = ''            
            this.campus.id = ''
            this.getCampuses()
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

      fd.append("campusImg", this.$refs.campusImg.files[0], this.$refs.campusImg.files[0].name)

      this.$axios.post(`/campus/update/image/${this.campus._id}`, fd, this.$headers())
        .then(res => {
          
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.getCampuses()
          }

          loader.hide()

        }).catch(err => {
          loader.hide()
          
          this.$toasted.error(err, {icon: "times-circle"})
        })

    },

    remove(id) {

      if(confirm("Remove campus?")) {
        let loader = this.$loading.show()

        this.$axios.delete(`/campus/remove/${id}`, this.$headers())
          .then(res => {
            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon: "times-circle"})
            } else {
              this.$toasted.success(res.data.message, {icon: "check"})
              this.getCampuses()
            }

            loader.hide()

          })
            .catch(err => {
              loader.hide()
              this.$toasted.error(err, {icon: "times-circle"})
            })
      }


    },

    selected(c) {
      this.campus = c
    }

  },
  computed: mapGetters(['campuses']),
  created() {
    this.getCampuses()
  }


}
</script>

<style scoped>

  .catImg {
    max-height: 400px;
    justify-content: center;
    padding-top: 10px;
  }

  .catImg img {
    max-height: 200px;
    max-width: 100%;
  }

</style>
