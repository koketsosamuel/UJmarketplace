<template>
  
  <div>

    <div class="row mb-4 px-3">

      <div 
        class="col item l4 s12 m6"
        v-for="ad in ads"
        :key="ad._id"
        @click="$router.push('/ad/'+ad._id)"
      >
        <div class="cursor mt-2">
          <div>
            <div class="img-cont flex purple">
              <img :src="baseUrl+ad.images[0].path" alt="" v-if="ad.images.length > 0">
              <p v-else class="py-5 m-0">
                <i class="fa fa-image fa-4x" aria-hidden="true"></i>
                <br>
                No image
              </p>
            </div>
            <div class="white-text orange p-1 darken-4">
              <p class="truncate">{{ad.name}}</p>
              <!-- <p class="truncate"><i class="fa fa-clock" aria-hidden="true"></i> {{getDate(ad.added)}} | <i class="fa fa-university" aria-hidden="true"></i> {{ad.campus}}</p> -->
              <p class="truncate">R {{ad.price}}</p>
            </div>

          </div>
        </div>
      </div>

    </div>

  </div>

</template>

<script>
import config from '../../config/config'

export default {

  name: "recentAds",

  props: ["category"],

  data() {

    return {

      ads: [],
      count: 0,
      baseUrl: null

    }

  },

  methods: {

    async getRecentAds() {

      let res = await this.$axios.get(`/ad/recent/${this.category || "none"}`)
      this.ads = res.data.ads
    },

    goTo(id) {
      this.$router.push("/ad/"+id)
    }

  },

  async created() {
    await this.getRecentAds()
    this.baseUrl = config.axiosConf.baseURL
  },

  watch: {

    $route: function () {
      this.getRecentAds()
      this.baseUrl = config.axiosConf.baseURL
    }

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
