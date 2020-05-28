<template>
  <div> 
    <div>


      <div class="container">

        <notifications />

        <categories-grid />

        <h5>&nbsp;Find it on your campus &#128515;</h5>
        <campus-grid />

        <div class="my-4 py-4"></div>

        <h5>&nbsp;Come on buy something &#128517;</h5>
        <random />

      </div>



    </div>


  </div>

</template>

<script>
import campGrid from '../layout/campusesGrid'
import catGrid from '../layout/categoriesGrid'
import random from '../ads/recentAds'

export default {

  name: "home",

  components: {
    "campus-grid": campGrid,
    "categories-grid": catGrid,
    random
  }

}
</script>

<style>

</style>
