<template>
  <div>

  <div class="container p-2 mt-2 purple mb-4 white-text">

    <h5 class="white-text">
      <i class="fa fa-user-plus d-inline"></i> Register
    </h5>


    <form @submit="register" autocomplete="off">

      <!-- NAME -->
      <div class="form-group">
        <label for="name"><i class="fa fa-tag" aria-hidden="true" ></i> Name</label>
        <input v-model="name" type="text" id="name" class="form-control" placeholder="e.g Koketso Maotomabe"  required>
      </div>


      <!-- STUDENT NO -->
      <div class="form-group">
        <label for="sNo"><i class="fa fa-graduation-cap" aria-hidden="true"></i> Student No. </label>
        <input  v-model="studentNumber" type="text" id="sNo" class="form-control" placeholder="e.g 212002355"  required>
      </div>

      <!-- CAMPUS -->
      <div class="form-group">
        <label for="campus"><i class="fa fa-building" aria-hidden="true"></i> Campus</label>
        <select  id="campus"  v-model="campus" required>
          <option v-for="camp in campusesFront" :key="camp._id" :value="camp.name">{{camp.name}}</option>
        </select>
      </div>

      <!-- PHONE -->
      <div class="form-group">
        <label for="cNo"><i class="fa fa-phone" aria-hidden="true"></i> Cellphone No.</label>
        <input v-model="cellNo" type="tel" id="cNo" class="form-control" placeholder="e.g 0712356212"  required>
      </div>

      <!-- WHATSAPP -->
      <div class="form-group">
        <label for="sWa"><i class="fab fa-whatsapp" aria-hidden="true"></i> WhatsApp No.</label>
        <input v-model="whatsApp" type="tel" id="sWa" class="form-control" placeholder="e.g 0623549212">
        <p class="m-0">
          <label>
            <input type="checkbox" class="filled-in" @change="whatsApp = cellNo"/>
            <span>Same as cellphone number</span>
          </label>
        </p>
      </div>


      <div class="form-group">
        <label for="pwd"><i class="fa fa-lock" aria-hidden="true"></i> Password</label>
        <input v-model="pwd" type="password" id="pwd" class="form-control" required autocomplete="off">
        <small id="helpName" class="text-muted">length should be atleast 4 charecters long</small>
      </div>

      <div class="form-group">
        <label for="pwd2"><i class="fa fa-lock" aria-hidden="true"></i> Confirm Password</label>
        <input v-model="pwd2" type="password" id="pwd2" class="form-control" required>
        <small id="helpName" class="text-muted">length should be atleast 4 charecters long</small>
      </div>

      <button class="btn orange btn-lg" type="submit" @click="submit()">Register</button>
    
    </form>

    <div class="alert mt-2 border-primary" role="alert"  >
      <span class="white-text"><router-link to="/login" class="white-text">Already registered? </router-link></span>
    </div>

  </div>

</div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex'

export default {

  name : "register",
  data() {
    return {

      name: '',
      campus: '',
      studentNumber: '',
      cellNo: '',
      email: '',
      whatsApp: '',
      pwd: '',
      pwd2: '',
      posOther: '',
      pos: 'student',
      campusesFront: [
        {name: "APK", _id: 0}, 
        {name: "APB", _id: 1}, 
        {name: "DFC", _id: 2}, 
        {name: "SWC", _id: 3}
      ]

    }
  },

  methods: {

    ...mapActions(['getCampuses']),

    register(e) {

      let position = this.pos

      e.preventDefault()

      // password matching
      if(this.pwd !== this.pwd2) {
        this.$toasted.error("Passwords do not match", {icon: "times-circle"})
        return false
      }

      // password length
      if(this.pwd.length < 4) {
        this.$toasted.error("Passwords should atleast be 4 charecters long", {icon: "times-circle"})
        return false
      }

      if(this.pos == 'other') {
        position = this.posOther
      }

      this.$axios.post('/user/register', {
        name: this.name,
        studentNo: this.studentNumber,
        email: this.studentNumber+"@student.uj.ac.za",
        password: this.pwd,
        whatsAppNo: this.whatsApp,
        cellNo: this.cellNo,
        campus: this.campus,
        pos: position
      }).then(res => {

        if(!res.data.error) {
          this.$toasted.success(res.data.message, {icon: "check-circle"})
          this.$router.push('/login')
        } else {
          this.$toasted.error(res.data.message, {icon: "times-circle"})
        }
        
      }).catch (err => {
        this.$toasted.error(err, {icon: "times-circle"})
      }) 

    },

    

  },

  computed: mapGetters(['campuses']),

  async created() {
    await this.getCampuses()
    if(this.campuses.length > 0) {
      this.campusesFront = this.campuses
    }
  }

}
</script>


<style scoped>

  .form {
    margin-bottom: 60px;
  }

  @media (max-width: 768px) {
    .form {

      margin-top: -60px !important;
      margin-bottom: 0px !important;

    }
  }

  label {
    color: #fff !important;
  }

  .m-0 {
    margin: 0px;
  }

</style>
