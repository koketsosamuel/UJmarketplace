<template>
  
  <div class="mt-2 SLIDER" v-if="notifications.length > 0">

    <hooper :itemsToShow="1" pagination="yes" :centerMode="true" :infiniteScroll="scroll" :autoPlay="true" :playSpeed="5000" :wheelControl="false">

      <slide v-for="(not, i) in notifications" :key="not._id" :index="i">
        <a :href="not.link" class="slideimg">
          <img :src="baseUrl+not.image" alt="">
        </a>
      </slide>

    </hooper>
        
  </div>

</template>

<script>
import config from "../../config/config"
import {Hooper, Slide} from "hooper"
import "hooper/dist/hooper.css"

export default {

  name: "notification",

  data: () => ({

    notifications: [],
    baseUrl: config.axiosConf.baseURL,
    scroll: false

  }),

  components: {
    hooper: Hooper,
    slide: Slide
  },

  methods: {

    async getNotifications() {

      this.$axios.get('/notification/all')
        .then(res => {
          
          this.notifications = res.data.notifications
          if(this.notifications.length > 1) {
            this.scroll = true
          }

        }).catch(err => {

          this.$toasted.error(err + "uu", {icon: "times-circle"})
        
        })
     
    }

  },

  async created() {

    await this.getNotifications()
    this.baseUrl = this.$baseUrl
    
  }

}
</script>

<style scoped>

  .slideimg img {
    width: 100%;
  }

  a {
    width: 100%;
    overflow: hidden;
  }

  .SLIDER {
    box-shadow: 0px 0px 6px #fff;
  }

  

  @media (min-width: 993px) {
    .hooper {
      height: 26.25vw;
    }
  }

  @media (max-width: 992px) {
    .hooper {
      height: 31.875vw;
    }
  }

  @media (max-width: 600px) {
    .hooper {
      height: 33.75vw;
    }
  }

</style>
