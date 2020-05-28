<template>
  <div>

     <div class="row mt-2">

        <!-- CATEGORY -->
        <div v-for="category in categories" :key="category._id" class="col s12 m4 l3">
          <router-link :to="'/browseads/1/'+category._id+'/all'">
            <div class="card">
                <div class="card-image">
                <img :src="baseUrl+category.image">
                <span class="card-title purple">{{category.name}}</span>
                </div>
            </div>
          </router-link>
        </div>
        <!-- CATEGORY END -->


      </div>

  </div>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import config from "../../config/config"

export default {


  name: 'categories',

  methods: mapActions(['getCategories']),

  computed: mapGetters(['categories']),

  data: () => ({
    baseUrl: config.axiosConf.baseURL
  }),

  created() {
    this.getCategories()
  }


}
</script>

<style scoped>

  .category {
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: #772953 15px 15px 10px;
    text-transform: capitalize;
  }

  .category:hover {
    box-shadow: #FF4500 0px 0px 10px;
  }

  @media (max-width: 320px) {
    .cat-sm {
      font-size: 0.8em;
      font-weight: lighter !important;
    }
  }

 


</style>
