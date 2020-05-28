<template>
  
<div>

  <div class="container p-2 mt-2 purple mb-4">

    <h5 class="white-text">
      <i class="fa fa-user  d-inline"></i> Profile
    </h5>
    <div class="clearfix"></div>

    <ul class="collapsible">

      <!-- PERSONAL DETAILS -->
      <li>

          <div class="collapsible-header">
            <p>
              Personal Details
            </p>
          </div>
          <div class="collapsible-body">
              <table class="table table-responsive">
                <tbody>
                  <tr>
                    <td>Name</td>
                    <td class="font-weight-light text-white font-italic">{{user.name}}</td>
                  </tr>
                  <tr>
                    <td>Campus</td>
                    <td class="font-weight-light text-white font-italic">{{user.campus}}</td>
                  </tr>
                  <tr>
                    <td>Position</td>
                    <td class="font-weight-light text-white font-italic">{{user.pos}}</td>
                  </tr>

                  <tr>
                    <td>Cell Number</td>
                    <td class="font-weight-light text-white font-italic">{{user.cellNo}}</td>                  

                  </tr>
                  <tr>
                    <td>WhatsApp Number</td>
                    <td class="font-weight-light text-white font-italic">{{user.whatsAppNo}}</td>                  
                  </tr>
                  <tr>
                    <td>Email</td>
                    <td class="font-weight-light text-white font-italic">{{user.email}}</td>
                  </tr>

                </tbody>
              </table>

          </div>
        <!-- /CONTACT DETAILS -->
      </li>

      <li>
        <!-- MANAGE ACC -->

          <div class="collapsible-header" role="tab" id="section2HeaderId">
            <p>
              Manage Account
            </p>
          </div>
          <div class="collapsible-body">
            <div class="card-body">            

              <div class="col-lg-8 col-md-12">

                <div class="card-panel orange darken-4 p-3 mb-4">
                  <p>Change Password</p>

                  <div class="form-group">
                    <label for="op">Old Password</label>
                    <input type="password" class="form-control"  id="op" v-model="oldPwd">
                  </div>

                  <div class="form-group">
                    <label for="np">New Password</label>
                    <input type="password" class="form-control" id="np" v-model="newPwd">
                  </div>

                  <button class="btn btn-block btn-dark" @click="changePwd">Change Password</button>
                </div>

                <div class="grad-2 border p-2">
                  

                  <a class="btn btn-block blue modal-trigger" href="#editProfile" @click="setUpdateVals(user)">
                    <i class="fa fa-pen" aria-hidden="true"></i> Edit Profile
                  </a>     
                  <a class="btn btn-block green mt-2 modal-trigger" href="#editPos" @click="setUpdateVals(user)">
                    <i class="fa fa-pen" aria-hidden="true"></i> Edit Position
                  </a> 
                </div>

              </div>

            </div>
          </div>

        <!-- /MANAGE ACC -->
      </li>

    </ul>
    <!-- /COLUMN CONTAINER -->

  </div>

    <!-- EDIT PROFILE -->
    <div class="modal purple" id="editProfile">

          <div class="modal-content">
            
            <!-- NAME -->
            <div class="form-group">
              <label for="name"><i class="fa fa-tag" aria-hidden="true"></i> Name</label>
              <input v-model="userUpdate.name" type="text" id="name" class="form-control" placeholder="e.g Koketso Maotomabe" aria-describedby="helpName" required>
            </div>

            <!-- CAMPUS -->
            <div class="form-group">
              <label for="campus"><i class="fa fa-university" aria-hidden="true"></i> Campus</label>
              <select class="form-control" id="campus"  v-model="userUpdate.campus" required>
                <option v-for="camp in campuses" :key="camp._id" :value="camp.name">{{camp.name}}</option>
              </select>
              <small id="helpName" class="text-muted">This is the campus where you are to be found</small>
            </div>

            <!-- PHONE -->
            <div class="form-group">
              <label for="cNo"><i class="fa fa-phone" aria-hidden="true"></i> Cellphone No.</label>
              <input v-model="userUpdate.cellNo" type="tel" id="cNo" class="form-control" placeholder="e.g 0712356212" aria-describedby="helpName" required>
              <small id="helpName" class="text-muted">This is the number people will use to call you when selling</small>
            </div>

            <!-- EMAIL -->
            <div class="form-group">
              <label for="sM"><i class="fa fa-envelope-o" aria-hidden="true"></i> Email</label>
              <input v-model="userUpdate.email" type="email" id="sM" class="form-control" placeholder="e.g whatever@example.com" aria-describedby="helpName" required>
            </div>

            <!-- WHATSAPP -->
            <div class="form-group">
              <label for="sWa"><i class="fab fa-whatsapp" aria-hidden="true"></i> WhatsApp No.</label>
              <input v-model="userUpdate.whatsAppNo" type="tel" id="sWa" class="form-control" placeholder="e.g 0623549212" aria-describedby="helpName">
              <small id="helpName" class="text-muted">This is the number people will use to WhatsApp you when you are selling</small>
            </div>


          </div>


          <div class="modal-footer">
            <button type="button" class="btn modal-close purple" data-dismiss="modal" @click="updateProfile">Update</button>
            <button type="button" class="btn modal-close white black-text" data-dismiss="modal">Cancel</button>
          </div>

    </div>

    <!-- EDIT POSITION -->
    <div class="modal fade green" id="editPos">
      
          <div class="modal-content">
            
            <div class="form-group">
              <label for="campus"><i class="fa fa-tag" aria-hidden="true"></i> Position</label>
              <select class="form-control" id="campus"  v-model="userUpdate.pos" required>
                <option value="student">Student</option>
                <option value="employee">Employee</option>
                <option value="other">Other (please specify)</option>
              </select>
              <small id="helpName" class="text-muted">This is what you are, in the <i class="fa fa-university" aria-hidden="true"></i></small>
            </div>

            <!-- IF OTHER -->
            <div class="form-group" v-if="userUpdate.pos == 'other'">
              <label for="other"><i class="fa fa-tag" aria-hidden="true"></i> Specify Position</label>
              <input v-model="posOther" type="tel" id="other" class="form-control" aria-describedby="helpName" spellcheck>
              <small id="helpName" class="text-muted">This is if you chose "other" as your position</small>
            </div>

          </div>


          <div class="modal-footer">
            <button type="button" class="btn modal-close green" data-dismiss="modal" @click="updatePos">Save</button>
            <button type="button" class="btn modal-close white black-text" data-dismiss="modal">Close</button>
          </div>

        </div>
      </div>

      
    <!-- /EDIT POSITION -->

  
</template>

<script>
import {mapGetters, mapActions} from 'vuex'

// exportation
export default {

  name: "profile",

  data() {
    return {

      user: {
        name: null,
        campus: null,
        pos: null,
        cellNo: null,
        whatsAppNo: null,
        email: null,
        studentNo: null,
        _id: null
      },

      oldPwd: "",

      userUpdate: {
        name: "null",
        campus: null,
        pos: null,
        cellNo: null,
        whatsAppNo: null,
        email: null,
        studentNo: null
      },

      newPwd: "",
      posOther: ""

    }

  },

  methods: {

    ...mapActions(['getCampuses', "DeAuth"]),

    getUser(){
      this.$axios.get('/user/profile', this.$headers())
        .then(res => {
        
          this.user = res.data.profile

        })
        .catch(err => {
          this.$toasted.error(err, {icon: "times-circle"})
        })
    },

    changePwd() {


      if(this.newPwd.length < 4 || this.oldPwd.length < 4) {
        this.$toasted.error("password must atleast be 4 charecters long", {icon: "times-circle"})
      } else {

        let loader = this.$loading.show()

        this.$axios.post('/user/passwordchange', {oldPwd: this.oldPwd, newPwd: this.newPwd},this.$headers()).then(res => {

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.oldPwd = ""
            this.newPwd = ""
          }

          loader.hide()

        })
        .catch(err => {
          this.$toasted.error(err, {icon: "times-circle"})
          loader.hide()
        })

      }
    },

    setUpdateVals(values) {
      this.userUpdate = values
    },

    updateProfile() {

      let loader = this.$loading.show()

      this.$axios.post('/user/profileupdate', {values: this.userUpdate, update: "profile"}, this.$headers()).then(res => {
        
        if(res.data.error) {
          this.$toasted.error(res.data.message, {icon: "times-circle"})
        } else {
          this.$toasted.success(res.data.message, {icon: "check"})
          this.getUser()
        }

        loader.hide()

      }).catch(err => {
        this.$toasted.error(err, {icon: "times-circle"})
        loader.hide()
      })
    },

    updatePos() {
      // carefully set postion
      let position = this.userUpdate.pos
      let positionOther = this.posOther

      function getPos() {


        if(position == 'other') {
          return positionOther
        } else {
          return position
        }
      }

      let loader = this.$loading.show()

      this.$axios.post('/user/profileupdate', {values: {pos: getPos()}, update: "position"}, this.$headers())
        .then(res => {

          if(res.data.error) {
          this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.getUser()
          }

          loader.hide()

        })
        .catch(err => {

          this.$toasted.error(err, {icon: "times-circle"})

          loader.hide()

        })

    },

    removeSelf() {

      let loader = this.$loading.show()

      this.$axios.delete('/user/rem/'+this.user._id, this.$headers())
        .then(res => {

          if(res.data.error) {
            this.$toasted.error(res.data.message, {icon: "times-circle"})
          } else {
            this.$toasted.success(res.data.message, {icon: "check"})
            this.DeAuth()
            this.$router.replace("/")
          }

          loader.hide()

        }).catch(err => {
          loader.hide()
          this.$toasted.error(err, {icon: "times-circle"})
        })

    }

  },
  // end of methods

  async created() {

    let loader = this.$loading.show()

    await this.getUser()
    await this.getCampuses()

    loader.hide()

  },

  async beforeMount() { 
    await this.getCampuses()
    await this.getUser()
    return false
  },

  computed: mapGetters(['campuses', 'authInfo'])


}
</script>

<style scoped>

</style>
