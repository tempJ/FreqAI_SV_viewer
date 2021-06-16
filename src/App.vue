<template>
  
  <v-app>
    <navi-bar
    @file="sendFile"
    />
    <v-main>
      <data-view
      :file="file"
      />
    </v-main>
  </v-app>
</template>

<script>
import NaviBar from './components/NaviBar';
import DataView from './components/DataView';

export default {
  name: 'App',

  components: {
    NaviBar,
    DataView,
  },

  data: () => ({
    file: null
  }),

  methods: {
    sendFile(e){
      this.file = e;
    }
  }
};
</script>

<style scoped>
</style>