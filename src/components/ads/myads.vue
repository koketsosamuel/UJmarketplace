<template>
  
  <div>

    <div class="container">

      <h3><i class="fas fa-box    "></i> My Ads</h3>

      <ul class="collection">
        <li
          class="collection-item purple white-text border-0"
          v-for="ad in myAds.ads"
          :key="ad._id"
        >
          <p class="m-0 left cursor" @click="viewAd(ad._id)">
            <i class="fa fa-tag"></i> {{ad.name}}&nbsp;| <i class="fa fa-calendar" aria-hidden="true"></i> <span class="font-italic"> Posted {{getDate(ad.added)}}</span>
          </p>

          <span class="right large-dev">
            <adManager :id="ad._id" :isDone="doneManaging" :auth="true" />
          </span>
          <div class="clearfix"></div>
        </li>
      </ul>

      <paginate
        v-if="myAds.pages > 1"
        :page-count="myAds.pages"
        :click-handler="gotopage"
        container-class="pagination mt-2"
        page-class="page-item blue white-text waves-effect"
        page-link-class="page-link white-text"
        prev-class="page-item "
        prev-link-class="page-link bg-dark text-white"
        next-class="page-item "
        next-link-class="page-link bg-dark text-white"
        active-class="blue darken-4"
        
      />

      <div v-if="myAds.ads.length == 0" class="purple mt-2 p-2 center">
        <p class="display-1 text-center">
          <i class="fa fa-cubes fa-4x" aria-hidden="true"></i>
          <br> No Ads
        </p>
      </div>

    </div>


  </div>

</template>

<script>

import {mapGetters, mapActions} from 'vuex'
import moment from 'moment'
import adManager from './adManager'
import jsCookies from 'js-cookie'

export default {
  name: "myads",
  data() {
    return {
      ad: {
        _id: null,
        name: null,
        images: [],
        price: null
      },

      id: null,

      page: 1

    }
  },
  components: {
    adManager
  },
  methods: {
    ...mapActions(['getMyAds']),

    viewAd(id) {
      this.$router.push('/ad/'+id)
    },

    getDate(date) {
      return moment(date).fromNow()
    },

    doneManaging() {
      this.getMyAds(this.page)
    },

    async gotopage(pageNum) {
      
      let loader = this.$loading.show()

      this.page = pageNum
      this.getMyAds(pageNum)

      loader.hide()

      scroll(0,0)
    }

  },
  computed: mapGetters(['myAds']),
  async created() {

    let loader = this.$loading.show()
    await this.getMyAds(1)
    loader.hide()

  }
}


</script>

<style>

  @media (max-width: 992px) {

    .large-dev {
      display: none !important;
    }

  }

</style>
