<template>
  <div>

    <div class="purple footer white-text mt-4">
      <div class="container">
        
        <div class="row">

          <div class="col s12 l12 purple darken-3 mt-2 p-2">
            <h5 class="orange-text"><b>UJ</b><small>marketplace</small> | <small class="white-text">For the students, by the students.</small></h5>
          </div>          

          <div class="col s12 l12 m12">

            <p>Keep looking. &#129488;</p>

            <div class="row">
              <div :class="'col s4 m4 l3 truncate center cursor '+randomColour()" @click="$router.push('/browseads/1/'+cat._id+'/all')" v-for="cat in categories" :key="cat._id">
                <p>{{cat.name}}</p>
              </div>
            </div>
          </div>

        </div>

      </div>
    </div>

  </div>
</template>

<script>
import {mapGetters, mapActions} from "vuex"

export default {
  
  name: "footerComponent",

  computed: mapGetters(['categories']),

  methods: {
    ...mapActions(['getCategories']),
    
    randomColour() {

      let colours = ["orange", "purple darken-3", "purple darken-2", "orange darken-2", "purple darken-4", "orange darken-4", "purple lighten-3"]
      return colours[Math.ceil(Math.random() * (colours.length - 1))]
    
    }
  },

  created() {
    this.getCategories()
  }

}
</script>

<style>

  

  @media (min-width: 988px) {

    .main-view {
      min-height: calc(100vh - 300px)
    }
  }

</style>
