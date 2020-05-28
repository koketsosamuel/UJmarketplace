<template>
    <div class="p-2 red lighten-4">
        <ul class="collapsible">
            <li  v-for="rep in reports" :key="rep._id" @click="report = rep">
                <div :class="'collapsible-header ' + red(rep.attended)" href="#reportMod">
                    <p>{{rep.category}}</p>
                </div>
                <div class="collapsible-body white">
                    <p class="card-panel green">{{rep.message}}</p>
                    <router-link :to="'/ad/'+rep.ad">The Ad</router-link> |&nbsp;
                    <a href="#!" @click="stroke(rep.adOwner)">Stroke</a> |&nbsp;
                    <a href="#!" @click="attended()">Mark Attended</a>

                </div>
            </li>
        </ul>

        <div v-if="reports.length == 0" class="purple p-2 mt-2">
            <p class="display-1 center  p-2 mt-2">
                <i class="fa fa-times-circle fa-4x" aria-hidden="true"></i>
            </p>
            <p class="center">No Reports</p>

        </div>
    </div>
</template>

<script>

export default {
    name: "adminReport",

    data: function() {
        return {
            reports: [],
            report: null,
            pages: null,
            page: 1,
            report: null
        }
    },
    methods: {

        async getReports(page) {


            let res = await this.$axios.get("/report/all/"+page, this.$headers())
            this.pages = res.data.pages
            this.page = res.data.page
            if(res.data.reports) {
                this.reports = res.data.reports
            }

        },

        red(attended) {
            if(attended) return ""
            return "red"
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

                    this.getReports(this.page)
                    this.attended()                    

                })
                .catch(err => {
                    loader.hide()
                    this.$toasted.error(err, {icon: "times-circle"})
                })

            }


        },

        attended() {

            let loader = this.$loading.show()

            this.$axios.post("/report/attend/"+this.report._id, {}, this.$headers())
                .then(res => {
                    loader.hide()

                    if(res.data.error) {
                        this.$toasted.error(res.data.message, {icon: "times-circle"})
                    } else {
                        this.$toasted.success(res.data.message, {icon: "check"})            
                    }

                    this.getReports(this.page)
                    //this.attended()                    

                })
                .catch(err => {
                    loader.hide()
                    this.$toasted.error(err, {icon: "times-circle"})
                })
                
        }

    },
    created() {
        this.getReports(1)
    }
}
</script>