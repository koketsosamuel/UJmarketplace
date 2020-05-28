<template>

    <div class="card">
        <div class="card-image">
        <img :src="image">
        <span class="card-title">{{title}}</span>
        </div>
    </div>

</template>

<script>
export default {

    props: {
        title: String,
        image: String
    },

    created() {
        alert(this.image)
    }

}
</script>

<style>

</style>