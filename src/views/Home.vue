<template>
  <div style="height: 100vh" class="d-flex justify-center align-center" >
    <v-card color="blue" dark class="mx-auto" max-width="344" max-height="400">
      <v-card-title class="text-h2 font-weight-medium">
        CURATORS
      </v-card-title>
      <v-card-text class="d-flex justify-center">
        <p class="text-h1">{{curatorNumber}}</p>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
  import axios from 'axios'

export default {
  name: 'Home',
  data: ()=>({
    curatorNumber: ''
  }),
  created() {
    this.initialize();
  },
  methods: {
    initialize() {
      axios.get('curators').then(resp => {
        if(resp.data == null && resp.data.length <= 0){
          this.curatorNumber = 'No curators now'
        }
        else{
          this.curatorNumber = resp.data.length
        }
      })
    }
  }


}
</script>
