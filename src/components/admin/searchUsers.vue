<template>
  
  <div class="green lighten-4 p-2 mt-2 container mb-4">

    <p><i class="fa fa-search" aria-hidden="true"></i> {{$route.params.search}}</p>

    <form class="form-inline my-2 my-lg-0" @submit="search" autocomplete="off">
      <input class="form-control mr-sm-2" type="search" v-model="searchUser" placeholder="Search" aria-label="Search" required>
      <button 
        class="btn green" 
        type="submit">
          <i class="fa fa-search" aria-hidden="true"></i>
      </button>
    </form>
    

    <ul class="collapsible">
        <li v-for="(user, i) in users" :key="i+user._id" @click="userObj = user">
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
      :page-count="Number(pages)"
      :click-handler="gotopage"
      container-class="pagination mt-4"
      page-class="page-item"
      page-link-class="page-link text-white bg-primary"
      prev-class="page-item"
      prev-link-class="page-link bg-dark text-white"
      next-class="page-item"
      next-link-class="page-link bg-dark text-white"
      active-class="active disabled font-weight-bold"      
      v-if="pages > 1"
    />

    <div v-if="users.length == 0" class="purple p-2 mt-2 center">
      <p class="display-1 center">
        <i class="fa fa-user-times fa-4x" aria-hidden="true"></i>
      </p>
      <p class="text-center mt-2 p-2">No Users</p>
    </div>

    

  </div>
</template>

<script>
import {mapGetters} from 'vuex'
import Vue from "vue"

export default {

  name: "searchusers",

  data() {
    return {
      users: [],
      page: 1,
      pages: 1,
      class: "",
      userObj: null,

      searchUser: ""
    }
  },

  methods: {

    async getSearchRes() {

      let loader = this.$loading.show()

      let res = await this.$axios.get(`/user/find/${this.$route.params.search}/${this.$route.params.page}`, this.$headers())
      
      this.pages = res.data.pages
      this.page = res.data.page
      this.users = res.data.users

      loader.hide()

    },

    search(e) {

      e.preventDefault()
      this.$router.push(`/search/user/${this.searchUser}/1`)

    },
    

    remove(id) {

      if(confirm("Are you sure you want to delete user?")) {

        let loader = this.$loading.show()

      

        this.$axios.delete(`/user/rem/${id}`, {
          headers: {
            Authorization: "Bearer " + this.$jsCookie.getJSON("4u7h3n71c4710n").token
          }
        })
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


    }

  },

  computed: mapGetters(['authInfo']),

  watch: {
    async $route() {
      let loader = this.$loading.show()
      await this.getSearchRes()
      loader.hide()
    }
  },

  async created() {

    let loader = this.$loading.show()
    await this.getSearchRes()
    loader.hide()

  }

}
</script>

<style scoped>

</style>
