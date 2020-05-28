<template>
  
  <div>

    <div class="container">

      <div class="purple p-4">

        <h5 class="white-text">
          <i class="fa fa-plus d-inline" aria-hidden="true"></i>&nbsp;Post Ad
        </h5>


        <!-- NAME -->
        <div>
          <label for="name"><i class="fa fa-tag" aria-hidden="true"></i> Name of Ad</label>
          <input type="text" id="name" v-model="post.name" class="form-control">
        </div>

        <!-- DESCR -->
        <div>
          <label for="desc"><i class="fa fa-tag" aria-hidden="true"></i> Description</label>
          <textarea id="desc" rows="4" class="form-control" v-model="post.description"></textarea>
        </div>

        <!-- CATEGORY -->
        <div>
          <label for="cat"><i class="fa fa-cube"></i> Category</label>
          <select id="cat" v-model="post.categoryId" class="form-select">
            <option v-for="category in categories" :key="category._id" :value="category._id">{{category.name}}</option>
          </select>
        </div>


        <!-- PRICE -->
        <div>
          <label for=""><i class="fa fa-money" aria-hidden="true"></i> Price</label>
          <input type="number" id="price" class="form-control" v-model="post.price" placeholder="e.g 122"> 
          <small id="fileHelpId" class="form-text text-muted">No need to put in the "R"</small>      
        </div>


        <!-- PICTURE -->
        <div class="d-none">
          <input type="file" class="form-control-file" ref="files" @change="fileSelected" multiple>
        </div>

        <div class="row text-center text-white px-3">
          
          <div class="btn" @click="uploadSelect">
            <span class="display-4 d-block"><i class="fa fa-camera-retro" aria-hidden="true"></i></span>
            Upload Images
          </div>
          
          <p class="text-muted">Upload atleast one image</p>

          <div class="card-panel" v-if="files.length > 0">
            <p>Images chosen</p>
            <ul class="border">
              <li
                v-for="(file, index) in files"
                :key="index"
                class="green"
              >
              <span class="left">
                [{{ index + 1 }}]&nbsp;
                {{file.name}}
                </span>
                <span class="btn red right" @click="removeFile(index)">
                  <i class="fa fa-trash" aria-hidden="true"></i>
                </span>
                <div class="clearfix"></div>
              </li>
            </ul>
          </div>

        </div>

        <button type="button" class="btn orange darken-4" @click="uploadPost">Post</button>

      </div>


    </div>

  </div>

</template>

<script>
import { mapGetters, mapActions } from 'vuex'
import jsCookie from 'js-cookie'

export default {


  name: "addPost",
  data(){
    return {
      fileSelected1: null,
      files: [],
      imageTypes: ["image/jpeg", "image/png", "image/gif"],
      post: {

        name: "",
        description: "",
        categoryId: "",
        price: null

      }
    }
  },
  methods: {

    ...mapActions(['getCategories', 'getCampuses']),

    checkAuth() {
      if(!this.authInfo.loggedIn) {
        this.$toasted.error("Please login first", {icon: "times-circle"})
        this.$router.replace("/login")
      }
    },

    fileSelected() {

      let files = this.$refs.files.files
      let unverified = [...files]
      let verified = []
      let typeError = false

      // validate type
      for(let i = 0; i < unverified.length; i++) {

        if(!this.imageTypes.includes(unverified[i].type)) {
          //console.log(unverified)
          typeError = true

        } else {
          verified.push(unverified[i])
        }

      }

      this.$refs.files.files = null

      // type error message
      if(typeError) {
        this.$toasted.error("Upload only valid images", {icon: "times-circle"})
      }

      this.files = [...this.files, ...verified]
        
      // remove excess
      if(this.files.length > 6) {

        this.$toasted.error("You can only upload a maximum of 6 images", {icon: "times-circle"})
        this.files.splice(6)
    
      }


    },

    uploadSelect() {

      if(this.files.length < 6) {
        this.$refs.files.click()
      } else {
        this.$toasted.error("You can only upload a maximum of 6 images", {icon: "times-circle"})
      }
    },

    uploadPost() {
      
      let loader = this.$loading.show()

      const fd = new FormData()
      
      for(let i = 0; i < this.files.length; i++) {
        fd.append("adImages", this.files[i], this.files[i].name)
      }

      Object.keys(this.post).forEach((key) => {
        fd.append(key, this.post[key])
      })


      this.$axios.post('/ad/add', fd, {headers: {Authorization: `Bearer ${jsCookie.getJSON('4u7h3n71c4710n').token}`}})
        .then(res => {

          loader.hide()

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon:"times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon:"check"})

            this.$router.push('/myads')
          }
        })
        .catch(err => {
          this.$toasted.error(err, {icon:"times-circle"})
          loader.hide()
        })
      
      
    },

    removeFile(index) {
      this.files.splice(index, 1)
    }


  },

  computed: mapGetters(['authInfo', 'categories', 'campuses']),

  async created() {
    
    await this.checkAuth()
    await this.getCategories()
    await this.getCampuses()

  }


}


</script>

<style scoped>

  .imageUpload {
    cursor: pointer;
    transition: all 0.3s;
    line-height: 100%;
    vertical-align: middle !important;
  }

  ul li {
    font-size: 12px;
    margin: 0px;
    min-height: 35px;
    padding: 1.5px;

    border: dashed 1px black;

  }

  ul {
    list-style: none;
    padding: 0;
  }

  .p-4 {
    padding: 20px
  }

  div.purple {
    margin-top: 20px;
  }

  .d-none {
    display: none;
  }

  

</style>
