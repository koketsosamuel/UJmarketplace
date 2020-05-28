<template>
  <div class="mt-2">


 
      <div class="dropdown-content" id="catOpts">
        <ul class="black-text">

            <li @click="category = 'all'; categoryName = 'all categories'">
              <a>All</a>
            </li>
            <li
              v-for="category_ in categories" 
              :key="category_._id"
              @click="category = category_._id; categoryName = category_.name"
            >
              <a>{{category_.name}}</a>
            </li>
 
        </ul>
      </div>
        
      <div class="dropdown-content" id="camOpts">
        <ul class="black-text">

            <li @click="campus = 'all'; campusName = 'all campuses'">
              <a>All</a>
            </li>
            <li
              v-for="campus_ in campuses" 
              :key="campus_._id"
              @click="campus = campus_._id; campusName = campus_.name"
            >
              <a>{{campus_.name}}</a>
            </li>
 
        </ul>
      </div>
        
    

        <div class="card-panel">
          <a href="javascript:void(0)" class="dropdown-trigger btn blue m-1" data-target="catOpts">{{categoryName}} <i class="fas fa-caret-down"></i></a> &nbsp;
          <a href="javascript:void(0)" class="dropdown-trigger btn blue m-1" data-target="camOpts">{{campusName}} <i class="fas fa-caret-down"></i></a>
        </div>



    <div class="row">

      <div 
        class="col item l4 s12 m6"
        v-for="ad in allAds.ads"
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
      v-if="allAds.pages > 1"
      :page-count="allAds.pages"
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

    <div v-if="allAds.ads.length == 0" class="center purple p-2 mt-2">
      <p class="text-center py-5">
        <i class="fa fa-box-open fa-4x" aria-hidden="true"></i>
        <br> No ads found
      </p>
    </div>



  </div>
</template>

<script>
import moment from 'moment'
import {mapActions, mapGetters} from 'vuex'

let opts = {
  page: 1,
  category: "all",
  campus: "all"
}

export default {

  name: "allAdds",
  data() {
    return {

      baseUrl: this.$baseUrl,
      page: 1,
      category: "all",
      campus: "all",
      categoryName: "all categories",
      campusName: "all campuses"

    }


  }, 

  computed: mapGetters(['categories', 'campuses', 'allAds']),

  methods: {

    ...mapActions(['getCategories', 'getCampuses', 'getAllAds']),

    getDate(date) {
      return moment(date).fromNow()
    },

    async gotopage(pageNum) {
      
      let loader = this.$loading.show()

      opts.page = pageNum
      await this.getAllAds(opts)
      loader.hide()

      scroll(0,0)
    },

    async getName(id, arr, obj, c) {

      if(id == "all") {
        obj.name = id + " " + c
      } else if(id.length < 12) {
        obj.name = id
        return false
      }

      await arr.forEach(element => {
        
        if(element._id == id) {          
          obj.name = element.name
        }

      })


    }

  },

  async created() {
    
    let loader = this.$loading.show()

    opts.page = await this.$route.params.page
    this.page = await this.$route.params.page

    opts.category = await this.$route.params.cat
    this.category = await this.$route.params.cat

    opts.campus = await this.$route.params.camp
    this.campus = await this.$route.params.camp

    await this.getAllAds(opts)
    await this.getCategories()
    await this.getCampuses()

    let obj = {}
    await this.getName(opts.campus, this.campuses, obj, "campuses")
    this.campusName = obj.name
    await this.getName(opts.category, this.categories, obj, "categories")
    this.categoryName = obj.name




    loader.hide()

  },

  watch : {
    
    category () {

      this.$router.push(`/browseads/1/${this.category}/${this.campus}`)
      scroll(0,0)

    },

    campus() {

      this.$router.push(`/browseads/1/${this.category}/${this.campus}`)
      scroll(0,0)

    },

    async $route () {
      let loader = this.$loading.show()

      opts.page = await this.$route.params.page
      this.page = await this.$route.params.page

      opts.category = await this.$route.params.cat
      this.category = await this.$route.params.cat

      opts.campus = await this.$route.params.camp
      this.campus = await this.$route.params.camp

      let obj = {}
      await this.getName(opts.campus, this.campuses, obj, "campuses")
      this.campusName = obj.name
      await this.getName(opts.category, this.categories, obj, "categories")
      this.categoryName = obj.name

      scroll(0,0)
      await this.getAllAds(opts)

      loader.hide()
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

  .m-1 {
    margin: 1.5px;
  }

  p {
    margin: 0px;
    
  }


</style>
