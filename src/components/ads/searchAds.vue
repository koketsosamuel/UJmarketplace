<template>
  
  <div>

    <div class="container mt-2">
      <p><i class="fa fa-search"></i> {{$route.params.search}} </p>

      <div class="row">
        <div 
        class="col item l4 s12 m6"
        v-for="ad in ads"
        :key="ad._id"
        @click="$router.push('/ad/'+ad._id)"
      >
        <div class="cursor mt-2">
          <div>
            <div class="img-cont flex purple">
              <img :src="$baseUrl+ad.images[0].path" alt="" v-if="ad.images.length > 0">
              <p v-else class="py-5 m-0">
                <i class="fa fa-image fa-4x" aria-hidden="true"></i>
                <br>
                No image
              </p>
            </div>
            <div class="white-text orange darken-4 p-1">
              <p class="truncate">{{ad.name}}</p>
              <p class="truncate"><i class="fa fa-clock" aria-hidden="true"></i> {{getDate(ad.added)}} | <i class="fa fa-university" aria-hidden="true"></i> {{ad.campus}}</p>
              <p class="truncate">R {{ad.price}}</p>
            </div>

          </div>
        </div>
      </div>
      </div>

      <paginate
      v-if="pages > 1"
      :page-count="pages"
      :click-handler="clickEr"
      container-class="pagination mt-2"
      page-class="page-item blue white-text waves-effect"
      page-link-class="page-link white-text"
      prev-class="page-item "
      prev-link-class="page-link bg-dark text-white"
      next-class="page-item "
      next-link-class="page-link bg-dark text-white"
      active-class="blue darken-4"
      
    />

      <div v-if="ads.length == 0" class="center purple p-2 mt-2">
        <p class="text-center py-5">
          <i class="fa fa-box-open fa-4x" aria-hidden="true"></i>
        </p>
        <p>No related results</p>
      </div>

    </div>


  </div>

</template>

<script>
import {mapGetters} from 'vuex'
import moment from 'moment'

export default {

  name: "searchAds",

  data() {
    return {
      ads: [],
      adsAll: [],
      perPage: 10,
      page: 1,
      pages: 1,
      class: ""
    }
  },

  methods: {

    async getSearchRes() {

      let loader = this.$loading.show()
      let res = await this.$axios.get("/ad/find/"+this.$route.params.search+"/"+ this.$route.params.page)
      
      this.pages = res.data.pages
      this.page = res.data.page
      this.ads = res.data.ads

      loader.hide()


    }, 

    async clickEr(p) {

      this.$router.push('/search/ad/'+this.$route.params.search+"/"+p)      

    },

    doneManaging() {
      this.getSearchRes()
    },

    getDate(date) {
      return moment(date).fromNow()
    },

  },

  computed: mapGetters(['authInfo']),

  watch: {
    async $route() {
      let loader = this.$loading.show()
      await this.getSearchRes()
      scroll(0,0)
      loader.hide()
    }
  },

  async created() {

    let loader = this.$loading.show()
    await this.getSearchRes()
    loader.hide()

  }

}
</script>

<style scoped>

  .img-cont {
    height: 200px !important;  
    overflow: hidden !important;
  }

  .img-cont img {
    max-width: 100%;
    max-height: 200px;
  }

  h6 {
    font-size: 16px !important;
  }

  .row {
    overflow: hidden;
  }

  .mt-2 {
    margin-top: 20px;
  }

  p {
    margin: 0px;
    
  }
</style>
