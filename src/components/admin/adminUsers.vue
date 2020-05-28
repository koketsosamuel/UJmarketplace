<template>
  <div class="green lighten-4 p-2">

    <form class="form-inline my-2 my-lg-0" @submit="search" autocomplete="off">
      <input class="form-control mr-sm-2" type="search" v-model="query" placeholder="Search" aria-label="Search" required>
      <button 
        class="btn green" 
        type="submit">
          <i class="fa fa-search" aria-hidden="true"></i>
      </button>
    </form>
    

    <ul class="collapsible">
        <li v-for="(user, i) in userS.users" :key="i+user._id">
          <div class="collapsible-header green white-text">
            <span class="left">
              <i class="fas fa-user"></i>{{user.name}} 
            </span>
          </div>
          <div class="collapsible-body">
              <p v-if="user._id == authInfo._id">Come on its you!!</p>
              <div class="data" v-else v-html="objectMarkup(user)"></div>
              <a class="btn grey modal-trigger" @click="stroke(user._id)">stroke</a>&nbsp;
              <a class="btn red modal-trigger" @click="remove(user._id)">Remove</a>
          </div>
        </li>
      </ul>

    <paginate
      :page-count="Number(userS.pages)"
      :click-handler="gotopage"
      container-class="pagination mt-4"
      page-class="page-item"
      page-link-class="page-link text-white bg-primary"
      prev-class="page-item"
      prev-link-class="page-link bg-dark text-white"
      next-class="page-item"
      next-link-class="page-link bg-dark text-white"
      active-class="active disabled font-weight-bold"      
      v-if="userS.pages > 1"
    />

    <div v-if="userS.users.length == 0" class="purple p-2 mt-2 center">
      <p class="display-1 center">
        <i class="fa fa-user-times fa-4x" aria-hidden="true"></i>
      </p>
      <p class="text-center mt-2 p-2">No Users</p>
    </div>

    

  </div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex'
import Vue from "vue"

export default {

  name: "adminUsers",
  data() {
    return {
    
      query: "",

      searchUser: "",

      sort: {joined: -1},

      userS: {
        users: [],
        pages: 1,
        page: 1
      }

    }
  },
  methods: {
    ...mapActions(['getUsers']),

    search(e) {

      e.preventDefault()
      this.$router.push(`/search/user/${this.query}/1`)

    },
    

    remove(id) {

      if(confirm("Are you sure you want to delete user?")) {

        let loader = this.$loading.show()

      

        this.$axios.delete(`/user/rem/${id}`, this.headers())
          .then(res => {

            loader.hide()

            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon: "times-circle"})
            } else {
              this.$toasted.success(res.data.message, {icon: "check"})         
              this.getUsers(this.users.page)
            }

          })
            .catch(err => {
              this.$toasted.error(err, {icon: "times-circle"})
              loader.hide()
            })

      }
    },

    async gotopage(pageNum) {
      let loader = this.$loading.show()
      await this.getUsers({page:pageNum, query: this.query, sort: this.sort})
      loader.hide()
    },

    objectMarkup(obj) {
      let str = ""

      Object.keys(obj).forEach((key) => {
        str += `<p style="padding:0px;margin:0px;">${key}: ${obj[key]}</p>`
      })

      return str

    },

    stroke(id) {

      if(confirm("Are you sure you want to stroke user?")) {
        
        let loader = this.$loading.show()

        this.$axios.put("/user/stroke/"+id,{}, this.$headers())
          .then(res => {

            loader.hide()
            if(res.data.error) {
              this.$toasted.error(res.data.message, {icon: "times-circle"})
            } else {
              this.$toasted.success(res.data.message, {icon: "check"})            
            }

          })
          .catch(err => {
            loader.hide()
            this.$toasted.error(err, {icon: "times-circle"})
          })

      }


    },
    
    async filter(arg) {
      
      this.query = {
        "banned": arg
      }
     
      if(arg == "none") {
        this.query = {}
      }
      
      await this.getUsers({page: 1, query: this.query, sort: this.sort})
      this.userS = await this.users

    }

  },
  computed: mapGetters(['users', 'authInfo']),
  async created() {
    
    await this.getUsers({page: 1, query: this.query, sort: this.sort})
    this.userS = await this.users

    Vue.nextTick(() => {
      this.$M.AutoInit()
    })

  }


}
</script>

<style scoped>



 p {
  margin: 0px !important;
  padding: 0px !important;
}

</style>
