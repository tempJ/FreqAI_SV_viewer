<template>
  
  <v-app>
    <navi-bar
    @file="sendFile"
    @config="sendConfig"
    />
    <v-main>
      <data-view
      :file="file"
      />
    </v-main>
    <v-footer
    class="footer"
    >
      <config-view
      :config="config"
      />
    </v-footer>
  </v-app>
</template>

<script>
import NaviBar from './components/NaviBar';
import ConfigView from './components/ConfigView';
import DataView from './components/DataView';

export default {
  name: 'App',

  components: {
    NaviBar,
    ConfigView,
    DataView,
  },

  data: () => ({
    file: null,
    config: null
  }),

  methods: {
    sendFile(e){
      this.file = e;
    },
    sendConfig(e){
      this.config = e;
    }
  }
};
</script>

<style scoped>
.footer {
  padding: 0px;
}
.config {
  padding: 0px;
  margin: 0px;
}
</style>