<template>
    <v-app id="inspire">
        <v-main>
            <NavigationBar/>
            <router-view/>
        </v-main>
    </v-app>
</template>


<script>
  import NavigationBar from "@/components/NavigationBar";
  export default {
    components: {NavigationBar}
  }
</script>

<style>
    #inspire img {
        height: 100%
    }
</style>
