<template>
  <div>

    <div class="box">

      <router-link :to="'/editpost/'+id">
        <button class="btn teal" title="Edit this ad"><i class="fa fas fa-pen"></i> <span class="sm"> Edit</span></button>
      </router-link>

      <router-link :to="'/managepics/'+id">
        <button class="btn grey darken-4" title="Add or remove images"><i class="fa fa-image"></i> <span class="sm"> Manage</span></button>
      </router-link>

      <button class="btn red darken-4" @click="remove()"><i class="fa fa-trash"></i> <span class="sm"> Remove</span></button>
    </div>


</div>
</template>

<script>
import authService from '../../services/auth'

export default {

  name: "adManager",
  props: ['id', 'isDone'],
  data() {
    return {

    }
  },
  methods: {

    remove() {

      if(confirm("Are you sure you want to delete it?")) {
        let loading = this.$loading.show()

        this.$axios.delete('/ad/rem/'+this.id, {
          headers: {
            Authorization: authService.getAxiosAuthHeader
          }
        }).then(res => {
          
          loading.hide()

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.isDone()
            this.$toasted.success(res.data.message, {icon: "check"})
          }
        }).catch(err => {

          this.$toasted.error(err, {icon: "times-circle"})
          loading.hide()        
        })

      }
    }

  }

}
</script>

<style scoped>

.box {
  padding:  10px;
}


</style>
