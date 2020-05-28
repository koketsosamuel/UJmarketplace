<template>
  <div>

    <div class="container mb-4">
      
      <h5><i class="fa fa-pen float-left d-inline" aria-hidden="true"></i> Admin</h5>

      
      <adminActions />
      <adminTotals />

      <div>

         <div class="row">
          <div class="col s12">
            
            <ul class="tabs">
              <li class="tab col s3 l2 white-text green"><a href="#test1" class="white-text active">Users</a></li>
              <li class="tab col s3 l3 teal"><a href="#test2" class="white-text">Categories</a></li>
              <li class="tab col s3 l2 red"><a  href="#test3" class="white-text">Reports </a></li>
              <li class="tab col s3 l2 brown"><a href="#test4" class="white-text">Campuses</a></li>
              <li class="tab col yellow darken-4 s3 l3 white-text"><a href="#test5" class="white-text">Notifications</a></li>
            </ul>
          </div>


          <div id="test1" class="col s12">
            <adminUsers />
          </div>

          <div id="test2" class="col s12">
            <adminCategories />
          </div>

          <div id="test3" class="col s12">
            <adminReports />
          </div>

          <div id="test4" class="col s12">
            <adminCampuses />
          </div>

          <div id="test5" class="col s12">
            <adminNotifications />
          </div>
        </div>


      </div>

    </div>

  </div>
</template>


<script>

import adminCategories from './adminCategories'
import adminCampuses from './adminCampuses'
import adminNotifications from './adminNotifications'
import adminReports from './adminReports'
import adminActions from './adminActions'
import adminTotals from './totals'
import adminUsers from './adminUsers'
import {mapActions} from 'vuex'

export default {

  name: "admin",
  data() {
    return {
      totals: {
        reports: 0,
        newReports: 0,
        notifications: 0,
        categories: 0,
        campuses: 4,
        messages: 0
      }
    }
  },

  components: {
    adminCategories,
    adminCampuses,
    adminNotifications,
    adminTotals,
    adminActions,
    adminUsers,
    adminReports
  },

  methods: mapActions(['getUsers']),

  async created() {
    let loader = this.$loading.show()
    await this.getUsers(1)
    let res = await this.$axios.get("/totals/", this.$headers())    
    this.totals = res.data
    loader.hide()
  },

  mounted() {
    this.$M.AutoInit()
  }
  

}
</script>

<style>



</style>
