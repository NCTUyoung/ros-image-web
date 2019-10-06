<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    >
      <v-card-title>
        Master IP :
        <v-text-field value="140.113.217.11" v-model="MasterURL"></v-text-field>
        Port :
        <v-text-field value="8888" v-model="port"></v-text-field>

      </v-card-title>

      <div class="my-2">
        <v-btn color="primary" v-on:click="connect()">Connect</v-btn>
        <p v-if="connected">connected!!</p>
      </div>
      <v-row
        class="d-flex"
        justify="center"
      >
        <v-menu
          v-model="showMenu"
          absolute
          offset-y
          style="max-width: 600px"
        >
          <template v-slot:activator="{ on }">
            <v-card
              class="portrait"
              :img=imageURL
              height="300"
              width="600"
              v-on="on"
            ></v-card>
          </template>

          <v-list>
            <v-list-item
              v-for="(item, index) in topicList"
              :key="index"
              @click="choose(item)"
            >
              <v-list-item-title>{{ item }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </v-row>
    </v-flex>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'

export default {
  components: {
    Logo,
    VuetifyLogo
  },
  data:()=>{
    return {
      showMenu:false,
      topicList:[],
      ros:null,
      MasterURL:'140.113.217.11',
      port:'8888',
      connected:false,
      chooseTopic:'/'
    }
  },
  methods:{
    connect(){
      this.ros = new ROSLIB.Ros({
        url : `ws://${this.MasterURL}:9090`
      });
      let vm = this
      this.ros.on('connection', function() {
        console.log('Connected to websocket server.');
        vm.connected = true
        vm.ros.getTopics((topics)=>{
          vm.topicList = topics.topics
        })
      });
    },
    choose(topic){
      this.chooseTopic = topic
    }
  },
  computed:{
    imageURL(){
      return 'http://'.concat(this.MasterURL).concat(`:${this.port}/stream?topic=`).concat(this.chooseTopic)
    }
  }
}
</script>
