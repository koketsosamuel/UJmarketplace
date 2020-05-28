<template>

    <div class="navbar-fixed">

         <!-- Dropdown Structure -->
        <ul id='dropdown1' class='dropdown-content' @mouseleave="$refs.blur.click()">
            
            <li v-if="authInfo.loggedIn">
                <router-link to="/myads">My Ads</router-link>
            </li>
            <li v-if="authInfo.loggedIn">
                <router-link to="/profile">Profile</router-link>
            </li>
            <li v-if="authInfo.loggedIn && authInfo.position == 'admin'">
                <router-link to="/admin">Admin</router-link>
            </li>
            <li v-if="!authInfo.loggedIn">
                <router-link to="/login">Login</router-link>
            </li>
            <li v-if="authInfo.loggedIn" @click="logout()">
                <a href="javascript:void(0)">Logout</a>
            </li>
            <li v-if="!authInfo.loggedIn">
                <router-link to="/register">Register</router-link>
            </li>

        </ul>

        <nav class="orange darken-4">
            <div class="nav-wrapper">
                <div class="container">

                    <router-link href="#" to="/" class="brand-logo hide-on-med-and-down" ref="logo"><b>UJ</b><small>marketplace</small></router-link>

                    <div class="hide-on-large row" ref="menuSmall">  
                        <div class="col s2">
                            <i class="fa fa-bars cursor" aria-hidden="true" @click="sideMenuToggle()"></i>
                        </div>  
                        <div class="col s8 center">
                            <router-link href="#" to="/" class="brand-logo" ref="logo"><b>UJ</b><small>marketplace</small></router-link>
                        </div>                    
                        <div class="col s2">
                            <a for="search" href="javascript:void(0)" class="right" @click="searchBarToggle()"><i class="fas fa-search"></i></a>
                            <div class="clearfix"></div>
                        </div>
                    </div>


                    <ul id="nav-mobile" class="right hide-on-med-and-down" ref="menu">

                        <li class="tooltipped" data-tooltip="Home" data-position="bottom">
                            <router-link to="/" exact>
                                <i class="fas fa-home"></i>
                            </router-link>
                        </li>

                        <li class="tooltipped" data-tooltip="Browse Ads" data-position="bottom">
                            <router-link to="/browseads/1/all/all">
                                <i class="fas fa-box"></i>
                            </router-link>
                        </li>

                        <li>
                            <a href="#" class="dropdown-trigger" data-target="dropdown1" @mouseover="$refs.userIcon.click()" ref="userIcon">
                                <i class="fas fa-user"></i>
                            </a>
 

                        </li>

                        <li class="tooltipped" data-tooltip="Post Ad" data-position="bottom">
                            <router-link to="/addPost" ><i class="fas fa-camera"></i></router-link></li>

                        <li class="tooltipped" data-tooltip="Search" data-position="bottom">
                            <a for="search" href="javascript:void(0)" class="label-icon" @click="searchBarToggle()"><i class="fas fa-search"></i></a>
                        </li>

                    </ul>

                    <div ref="searchForm" class="d-none">
                        <div class="input-field">
                            <input type="search" name="search" id="search" class="orange darken-4" v-model="query" required ref="search" autofocus @keydown.enter="search()" @blur="searchBarToggle()">
                            <label for="search" class="label-icon white-text"><i class="fa fa-search white-text" aria-hidden="true"></i></label>
                            <i class="fa fa-times close white-text cursor" @click="searchBarToggle()"></i>
                            <div class="clearfix"></div>
                        </div>
                    </div>

                </div>
            </div>
        </nav>

        <div>

        <div class="p-2 purple sideNav hide-on-large d-none" ref="sideMenu">
            <span class="White-text header left" @click="sideMenuToggle()"><b>UJ</b><small>marketplace</small></span>
            <button class="btn orange darken-4 right" @click="sideMenuToggle()"><i class="fa fa-times-circle" aria-hidden="true"></i></button>
            <div class="clearfix"></div>

            <div class="collection" @click="sideMenuToggle()">
                
                <router-link to="/" class="collection-item" exact><i class="fa fa-home" aria-hidden="true" exact></i> Home</router-link>

                <router-link to="/browseads/1/all/all" class="collection-item">
                    <i class="fas fa-box"></i> Browse Ads
                </router-link>
      
                <router-link to="/addPost" class="collection-item"><i class="fas fa-camera"></i> Post Ad</router-link>

                <router-link v-if="authInfo.loggedIn" class="collection-item" to="/myads"><i class="fas fa-box    "></i> My Ads</router-link>
               
                <router-link class="collection-item" to="/profile"  v-if="authInfo.loggedIn"><i class="fas fa-user-circle    "></i> Profile</router-link>
             
             
                <router-link class="collection-item" to="/admin" v-if="authInfo.loggedIn && authInfo.position == 'admin'"><i class="fas fa-user-ninja    "></i> Admin</router-link>
         
                <router-link class="collection-item" to="/login" v-if="!authInfo.loggedIn"><i class="fas fa-sign-in-alt    "></i> Login</router-link>
                
                <a href="javascript:void(0)" class="collection-item" v-if="authInfo.loggedIn" @click="logout()"><i class="fas fa-sign-out-alt    "></i> Logout</a>
              
                <router-link class="collection-item" to="/register" v-if="!authInfo.loggedIn"><i class="fas fa-user-plus    "></i> Register</router-link>
               

            </div>


        </div>
        <div class="overlay hide-on-large d-none" ref="overlay" @click="sideMenuToggle()"></div>
        <div class="" ref="blur"></div>
        <router-link to="/addPost" class="btn-floating purple btn-lg pulse hide-on-large">
            <i class="fas fa-camera"></i>
        </router-link>

    </div>

        
    </div>

</template>

<script>
import Vue from "vue"
import config from "../../config/config"
import { mapGetters, mapActions } from 'vuex'

export default {
    
    name: "NavBar",

    data: () => ({
        query: ""
    }),

    methods: {

        ...mapActions(['DeAuth']),

        logout() {
            this.DeAuth()
            this.$router.push("/")
            this.$toasted.success("Logged Out", {icon: "check"})
        },

        async searchBarToggle() {
            if(this.$refs.searchForm.className == "d-none") {
                this.$refs.searchForm.className = await ""
                this.$refs.logo.className = "d-none"
                this.$refs.menu.className = "d-none"
                this.$refs.menuSmall.className = "d-none"
                this.$refs.search.focus()
            } else {
                this.$refs.menu.className = "right hide-on-med-and-down"
                this.$refs.logo.className = "brand-logo"
                this.$refs.menuSmall.className = "hide-on-large row"
                this.$refs.searchForm.className = "d-none"
            }
        },

        sideMenuToggle() {

            let sideMenuClass = "p-2 purple sideNav hide-on-large"

            if(this.$refs.sideMenu.classList == sideMenuClass) {
                this.$refs.sideMenu.classList = (sideMenuClass + " sideNav-hide")
                this.$refs.overlay.classList = "d-none"
            } else {
                this.$refs.sideMenu.classList = sideMenuClass
                this.$refs.overlay.classList = "overlay hide-on-large"
            }

        },

        search() {
            this.$router.push("/search/ad/"+this.query+"/1")
            this.searchBarToggle()
            this.query = ""
        }
    },

    computed: mapGetters(['authInfo']),

    created() {
        Vue.nextTick(() => {
            this.$refs.searchForm.className = "d-none"
        })
    }
 
}


</script>

<style scoped>

    small {
        font-weight: lighter;
    }

    .btn-floating {
        position: fixed;
        bottom: 20px;
        right: 20px;
    }

    .close {
        position: absolute;
        top: 0px;
        right: 0px;
    }

    @media (min-width: 992.1px) {
        .hide-on-large {
            display: none;
        }
    }

    @media (max-width: 600px) {
        .close {
            top: 13px;
        }
    }

    .sideNav {
        position: fixed;
        top: 0px;
        left: 0px;
        width: 70%;
        height: 100%;
        z-index: 99999999999;
        transition: all 0.3s;
        transform: translateX(0%);
        overflow-y: auto;
    }

    .sideNav-hide {
        transform: translateX(-100%) !important;
    }

    .overlay {
        position: fixed;
        top: 0px;
        left: 0px;
        width: 100%;
        height: 100%;
        z-index: 99999998;
        background: rgb(0, 0, 0);
        opacity: 0.6;
        transition: all 0.3s;
    }

    .overlay-hide {
        display: none;
    }

    .header {
        font-size: 25px;
        color: aliceblue;
        cursor: pointer;
    }

</style>
