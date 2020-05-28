<template>
  <div class="p-2 teal lighten-4">

    <!-- CATEGORIES LIST -->
    <div class="collection" v-if="categories.length > 0">
     
        <div class="collection-item" v-for="category in categories" :key="category._id" @click="selected(category)"> 

          <span class="left">{{category.name}}</span>

          <button class="btn red right" @click="remove(category._id)"><i class="fa fa-trash" aria-hidden="true"></i> </button>
          <a href="#categoryImage" class="btn teal right small modal-trigger"><i class="fa fa-image" aria-hidden="true"></i> </a>
          <a href="#categoryEdit" class="btn green right modal-trigger" ><i class="fa fa-pen" aria-hidden="true"></i> </a>
          <div class="clearfix"></div>

        </div>
    </div>

    <!-- CATEGORY EDIT MODAL -->
    <div id="categoryEdit" class="modal teal">
      <div class="modal-content">
        <div>
          <h5 class="white-text">Edit Category</h5>
          <p>Name</p>
          <input type="text" placeholder="Name" v-model="category.name">
          <p>Expiration Days</p>
          <input type="text" placeholder="Expiration Days" v-model="category.expirationDays">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="update()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>

    <!-- CATEGORY IMAGE UPDATE -->
    <div id="categoryImage" class="modal teal">
      <div class="modal-content">
        <div>
          
          <h5 class="white-text">Update Category Image</h5>

          <div class="catImg">
            <img :src="this.$baseUrl+category.image" alt="">
          </div>
 
          <input type="file" name="" ref="catImg">

        </div>
      </div>
      <div class="modal-footer orange darken-4">
        <a href="#!" class="modal-close btn teal" @click="updateImage()">Update</a>&nbsp;
        <a href="#!" class="modal-close btn white black-text">Cancel</a>
      </div>
    </div>


    <div v-if="categories.length == 0">
      <h1 class="display-1 text-center">
        <i class="fa fa-times-circle" aria-hidden="true"></i>
      </h1>
      <h4 class="text-center">No Categories</h4>
    </div>

    

  </div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex'


export default {

  name: "adminCategories",
  data() {
    return {
      category: {
        name: '',
        _id: '',
        expiration: null,
        expirationDays: null,
        image: ""
      }
    }
  },
  methods: {
    ...mapActions(['getCategories']),

    update() {
      
      let loader = this.$loading.show()

      this.$axios.put(`/category/update/${this.category._id}`, this.category, this.$headers())
        .then(res => {
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.category.name = ''            
            this.category.id = ''
            this.getCategories()
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

      fd.append("categoryImg", this.$refs.catImg.files[0], this.$refs.catImg.files[0].name)

      this.$axios.put(`/category/update/image/${this.category._id}`, fd, this.$headers())
        .then(res => {
          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.getCategories()
            this.$refs.catImg.files = null
          }

          loader.hide()

        }).catch(err => {
          loader.hide()
          this.$toasted.error(err, {icon: "times-circle"})
        })

    },

    remove(id) {

      if(confirm("Remove category?")) {
        let loader = this.$loading.show()

        this.$axios.delete(`/category/remove/${id}`, this.$headers())
          .then(res => {
            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon: "times-circle"})
            } else {
              this.$toasted.success(res.data.message, {icon: "check"})
              this.category.name = ''
              this.category.icon = ''
              this.category.id = ''
              this.getCategories()
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
      this.category = c
    }

  },
  computed: mapGetters(['categories']),
  created() {
    this.getCategories()
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
