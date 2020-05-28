<template>
  
  <div>

    <div class="">
      
      <div class="row center white-text p-0">

        <div class="col l3 m3 s4">
          <div class="card green p-2 h-100">
            <div>
              <p>Users</p>
              <i class="fa fa-users fa-2x"></i>
              <p>{{totals.users}}</p>
            </div>
          </div>
        </div>


        <div class="col l3 m3 s4">
          <div class="card blue p-2 h-100">
            <div>
              <p>Ads</p>
              <i class="fa fa-archive fa-2x"></i>
              <p>{{totals.ads}}</p>
            </div>
          </div>
        </div>

        <div class="col l3 m3 s4">
          <div class="card brown p-2 h-100">
            <div>
              <p>Campuses</p>
              <i class="fa fa-university fa-2x"></i>
              <p>{{totals.campuses}}</p>
            </div>
          </div>
        </div>

        <div class="col l3 m3 s4">
          <div class="card teal p-2 h-100">
            <div>
              <p>Categories</p>
              <i class="fa fa-align-left fa-2x"></i>
              <p>{{ totals.categories }}</p>
            </div>
          </div>
        </div>

        <div class="col l3 m3 s4">
          <div class="card yellow darken-4 p-2 h-100">
            <div>
              <p>Notifications</p>
              <i class="fa fa-bell fa-2x" aria-hidden="true"></i>
              <p>{{ totals.notifications }}</p>
            </div>
          </div>
        </div>

        <div class="col l3 m3 s4">
          <div class="card red p-2 h-100">
            <div>
              <p>New Reports</p>
              <i class="fa fa-boxes fa-2x" aria-hidden="true"></i>
              <p>{{ totals.newReports }}</p>
            </div>
          </div>
        </div>

        <div class="col l6 m6 s4">
          <div class="card green lighten-2 p-2 h-100">
            <div>
              <p>Google Analytics</p>
              <i class="fab fa-google fa-2x" aria-hidden="true"></i>
              <p>-</p>           
            </div>
          </div>
        </div>


      </div>

      

    </div>

  </div>

</template>

<script>
import {mapGetters} from 'vuex'

export default {

  name: "totals",

  data() {
    return {

      totals: {
        ads: "N/A",
        campuses: "N/A",
        categories: "N/A",
        users: "N/A",
        notifications: "N/A",
        newReports: "N/A"
      }

    }
  },

  computed: mapGetters(['authInfo']),

  async created() {

    let res = await this.$axios.get("/totals/", this.$headers())    
    this.totals = res.data

    if(!this.totals.reports) {
      this.totals.reports = 0
    }

  }

}
</script>

<style>

</style>
