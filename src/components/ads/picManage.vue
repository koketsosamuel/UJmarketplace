<template>
  
  <div>

    <div class="purple p-2 mt-2 container mb-4">
      
      <h5 class="white-text">
        <i class="fa fa-image d-inline" aria-hidden="true"></i>&nbsp;Pictures
      </h5>

      <div class="row">
        <div class="col m4 s6 p-0 px-3 my-2" v-for="(image, i) in images" :key="i">

          <div class="flex h-100 grad-4 border border-primary border-2">
            <!-- <img :src="imagePaths[i]" :alt="image.name" @click="index = i"> -->
            <vImage :src="imagePaths[i]" />
          </div>

          <div class="rem">
            <button class="btn red" @click="remPic(image, i)"><i class="fa fa-trash" aria-hidden="true"></i></button>
          </div>

        </div>
      </div>

      <div class="orange p-2 center mb-2 darken-4" v-if="images.length == 0">
        <p class="text-center">
          <i class="fa fa-images fa-3x" aria-hidden="true"></i>
          <br> No Images
        </p>
      </div>


      <!-- PICTURE -->
      <div class="d-none">
        <input type="file" class="form-control-file" ref="files" @change="addPic">
      </div>


      <button class="btn btn-block orange my-2 darken-4" @click="$refs.files.click()" v-if="images.length < 6">
        <i class="fa fa-camera-retro" aria-hidden="true"></i> Add Picture
      </button>

      <!-- GALLERY COMPONENT -->
      <vueGallery :images="imagePaths" :index="index" @close="index = null"/>

    </div>

  </div>

</template>

<script>
import {mapGetters, mapActions} from 'vuex'
import config from '../../config/config'
import vImage from 'v-lazy-image'
import vueGallery from 'vue-gallery-slideshow'
import { setTimeout } from 'timers';

export default {

  name: "adManage",

  data(){
    return {
      
      urlBase: config.axiosConf.baseURL,

      adv: {
        name: null,
        images: [],
        _id: null
      },

      imagePaths: [],

      images: [],

      index: null,

      imageTypes: ["image/jpeg", "image/png", "image/gif"],

      axiosOpts: null


    }
  },

  components: {
    vueGallery,
    vImage
  },

  methods: {

    ...mapActions(['getAd']),

    addPic() {

      let file = this.$refs.files.files[0]
      let typeError = false

      // validate type
      if(!this.imageTypes.includes(file.type)) {
        //console.log(unverified)
        typeError = true
      }

      // type error message
      if(typeError) {
        this.$toasted.error("Upload only valid images", {icon: "times-circle"})
      } else {

        let fd = new FormData
        fd.append("image", file, file.name)

        let loader = this.$loading.show()

        this.$axios.put("/ad/addpic/"+this.$route.params.adId,fd, this.axiosOpts).then(res => {

            loader.hide()

            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon:"times-circle"})
            } else {
              
              setTimeout(() => {
                this.images.push(res.data.image)
                this.imagePaths.push(config.axiosConf.baseURL+res.data.image.path)
              }, 1000)

              this.$toasted.success(res.data.message, {icon:"check"})
            }
          })
          .catch(err => {
            this.$toasted.error(err, {icon:"times-circle"})
            loader.hide()
          })
      } 

    }, 

    async init() {
      await this.getAd(this.$route.params.adId)
      this.adv = await this.ad
      this.images = await this.ad.images
      this.imagePaths = []

      for(let i = 0; i < this.images.length; i++) {
        this.imagePaths.push(config.axiosConf.baseURL+this.images[i].path)
      }
    }, 

    async remPic(image, index) {

      let loader = this.$loading.show()

      this.$axios.post("/ad/rempic/"+this.$route.params.adId, {index, imgUrl: image.path}, this.axiosOpts)
        .then(res => {

          if(res.data.error) {

            this.$toasted.error(res.data.message, {icon: "times-circle"})

          } else {

            this.images.splice(index, 1)
            this.imagePaths.splice(index, 1)
            this.init()
            this.$toasted.success(res.data.message, {icon: "check"})

          }

          loader.hide()

        }).catch(err => {

          loader.hide()

          this.$toasted.error(err, {icon: "times-circle"})

        })

    }

  },

  computed: mapGetters(['ad', 'authInfo']),

  async created() {
    
    let loader = this.$loading.show()
    await this.init()
    this.axiosOpts = this.$headers()
    loader.hide()

  }

}
</script>

<style scoped>

.flex img {
    max-width: 99%;
    max-height: 148.5px !important;
}

.flex {
  height: 150px !important;
  border: 2px #6e047c solid;
}

.rem {
  z-index: 1000 !important;
  position: relative;
  top: 0px;
  margin-bottom: 7px;
}



</style>
