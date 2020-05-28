<template>
  <div>

     <div class="row">

        <!-- <div class="col s12 m4 l3">
          <router-link to="/browseads/1/all/all">
            <div class="card">
                <div class="card-image">
                  <img src="../../../public/assets/3d0105.jpg">
                  <span class="card-title purple">All Campuses</span>
                </div>
            </div>
          </router-link>
        </div> -->

        <!-- campus -->
        <div v-for="campus in campuses" :key="campus._id" class="col s12 m6 l3">
          <router-link :to="'/browseads/1/all/'+campus.name">
            <div class="card">
                <div class="card-image">
                <img :src="baseUrl+campus.image">
                <span class="card-title purple">{{campus.name}}</span>
                </div>
            </div>
          </router-link>
        </div>
        <!-- campus END -->


      </div>

  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import config from "../../config/config"

export default {


  name: 'campuses',

  methods: mapActions(['getCampuses']),

  computed: mapGetters(['campuses']),

  data: () => ({
    baseUrl: config.axiosConf.baseURL
  }),

  created() {
    this.getCampuses()
  }


}
</script>

<style scoped>

  a {
    padding : 0px !important;

  }

</style>
