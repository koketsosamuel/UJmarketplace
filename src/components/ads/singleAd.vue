<template>
  
  <div>

    <div class="container">

      <div class="card-panel purple white-text">
        <p>{{adv.name}}</p>
        <p><i class="fas fa-clock"></i> Posted {{posted}} | <i class="fa fa-university" aria-hidden="true"></i> {{adv.campus}}</p>
        <p>R {{adv.price}}</p>
      </div>
      
      <div class="row blue p-2">

          <div class="card blue col l8 s12 main cursor">
            <div class="flex cursor">
              <img class="cp" v-if="images.length > 0" :src="images[0]" alt="" @click="index = 0">
              <span v-else class="p-4 center"><i class="fa fa-image fa-4x"></i> <br> No image</span>
              <div class="overlay" v-if="images.length > 1">1 / {{images.length}}</div>
            </div>

          </div>
       
      
          <div class="col s12 l4 side">            

            <div class="card-panel">
              <p class="green-text"><i class="fab fa-whatsapp"></i> {{seller.whatsAppNo}}</p>    
              <p class="blue-text">
                <a :href="'tel:'+seller.cellNo"><i class="fa fas fa-phone"></i> {{seller.cellNo}}</a>
              </p>   
              <p class="blue-text text-darken-4">
                <a :href="'mailto:'+seller.email"><i class="fa fab fa-envelope"></i> {{seller.email}}</a>
              </p>                     
            </div>

            <div class="purple lighten-3">
              <adManager 
                :id="adv._id" 
                :isDone="whenDone" 
                v-if="authInfo.loggedIn && (authInfo._id == adv.seller || authInfo.position == 'admin')" 
              />
            </div>

            <!-- <button class="btn btn-primary mt-3" @click="share"><i class="fa fa-share" aria-hidden="true"></i> Share</button> -->
            <a href="#reportMod" class="btn block red modal-trigger mt-2">Report</a>
            
          </div>


      
      </div>
            <div class="card-panel yellow darken-4">
              
              <p><b>Description: </b></p>
              <p v-if="adv.description.length != 0">{{adv.description}}</p>
              <p v-else>None given.</p>

            </div>

      <div class="modal purple" id="reportMod">
        <div class="modal-content">
          <p>Category</p>
          <select v-model="report.category">
            <option value="Nudity">Nudity</option>
            <option value="Drugs">Drugs</option>
            <option value="Scam">Scam</option>
            <option value="Violence">Violence</option>
            <option value="Other">Other</option>
          </select>

          <p>More description</p>
          <textarea v-model="report.message"></textarea>

          <p>Your Email</p>
          <input v-model="report.reporter">

        </div>
        <div class="modal-footer">
          <button class="modal-close btn purple" @click="reportMake()">
            Report</button>
          <button class="modal-close btn white black-text">
            Cancel
          </button>
        </div>
      </div>

      <vueGallery :images="images" :index="index" @close="index = null"/>
    
     

      <recentAds :category="adv.category" />

    </div>

  </div>

</template>

<script>
import {mapGetters, mapActions} from 'vuex'
import config from '../../config/config'
import authService from '../../services/auth'
import vueGallery from 'vue-gallery-slideshow'
import adManager from './adManager'
import getDate from '../../services/dateGet'
import recentAds from './recentAds'

export default {

  name: "singlead",

  components: {
    vueGallery,
    adManager,
    recentAds
  },

  data() {
    return {
      adv: {
        name: '',
        campus: '',
        price: '',
        description: '',
        quantity: null,
        _id: null,
        seller: null,
        category: null
      },

      seller: {
        pos: null,
        email: '',
        whatsAppNo: null,
        cellNo: null
      },

      images: [],

      category: {
        name: null
      },

      report: {
        category: "",
        message: "",
        reporter: "",
        ad: null,
        adOwner: null
      },

      index: null,

      posted: "",

      authorised: false

    }
  },

  methods: {

    ...mapActions(['getAd']),

    setImages(imageArr) {

      this.images = []

      for(let i = 0; i < imageArr.length; i++) {
        this.images.push(config.axiosConf.baseURL+imageArr[i].path)
      }
    },

    async getCategory(cat) {
      let res = await this.$axios.get('/category/one/'+cat)
      this.category.name = res.data.category.name
      this.category.icon = "fas fa fa-" + res.data.category.icon
    },

    whenDone() {
      this.$router.replace("/")
    },

    getSeller(id) {
      this.$axios('/user/seller/'+id)
        .then(res => {

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {

            this.seller = res.data.seller

          }

        }).catch(err => {
          // toast
          this.$toasted.error(err,{icon: "times-circle"})
        })
    },

    async postLoad() {
      await this.getAd(this.$route.params.adId)
      this.adv = await this.ad
      await this.getCategory(this.adv.category)
      await this.setImages(this.ad.images)
      await this.getSeller(this.adv.seller)
      this.posted = await getDate(this.adv.added)
    },

    async reportMake() {
      this.report.ad = this.ad._id
      this.report.adOwner = this.ad.seller

      if(this.report.category == "" || this.report.reporter == "") {this.$toasted.error("Insufficient Information", {icon:"times-circle"})} else {

        let res = await this.$axios.post("/report/add", this.report)
        if (res.data.error) {
          this.$toasted.error(res.data.message, {icon: "times-circle"})
        } else {
          this.$toasted.success(res.data.message, {icon: "check"})     
          this.report.message = ""
          this.report.reporter = ""
        }

      }
    }


  },

  computed: mapGetters(['authInfo', 'ad']),


  async created(){

    let loader = this.$loading.show()
    await this.postLoad()
    loader.hide()

  },

  watch: {
    async $route() {
    
      let loader = this.$loading.show()

      await this.postLoad()
      
      scroll(0,0)

      loader.hide()

    }
  }


}
</script>

<style scoped>

  img {
    transition-property: all!important;
    transition-duration: 0.3s!important;
  }

  .card {
    border-radius: 0px !important;
  }

  .alert {
    text-shadow: none;
  }


  .carousel img {
    width: auto;
    max-width: 100%;
    max-height: 100%;

  }

  .modal-body {
    height: 70vh !important;
    overflow: hidden;
  }

  

  @media (min-width: 992px) {
    .card-info {
      position: absolute !important;
      top: 0px !important;
      width: 100%;
      padding-bottom: 20px;
    }

    .card:hover .card-info {
      opacity: 0.5;
    }

    .card {
      max-height: 70vh !important;
      overflow: hidden;
    }

    .card img {
      max-height: 56vh;
      max-width: 100%;
    }

    /* .card-info-sm {
      display: none;
    } */
  }

/* 
  .flex {
    min-height: 300px !important;
    max-height: 350px !important;
  } */
  

  @media (max-width: 991.5px) {
    .card {
      max-height: 100% !important;
      overflow: hidden;
    }

    .card img {
      max-height: 200px;
      max-width: 100%;
    }

    .card.main {
      box-shadow: 0 0 0 #000 !important;
    }

    .card-info {
      display: none;
    }

    /* .flex {
      min-height: 100% !important
    } */
  
    .p-2 {
      padding: 10px;
    }

    
  }

  @media (min-width: 992px) {

      .flex {
        height: 100% !important;
      }

      .main {

        height: 300px !important;
        max-height: 400px !important;
        display: block;
      }
    
  }

  .card-panel {
    display: block !important;
    width: 100% !important;
    padding-top: 10px;
    padding-bottom: 10px;
  }

  .overlay {
    position: absolute;
    bottom: 0px;
    left: 0px;
    width: 100%;
    background: rgba(0, 0, 0, 0.644);
    color: white;
    padding: 6px;
    text-align: center;
  }

  

</style>
