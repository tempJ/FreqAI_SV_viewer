<template>
  
  <v-app>
    <navi-bar
    @file="sendFile"
    @config="sendConfig"
    @clear="sendClear"
    @snack="getSnack"
    />
    <v-main>
      <data-view
      :file="file"
      :clear="clear"
      />
    </v-main>

    <v-snackbar
    shaped
    :color="(snack.suc === 0)? 'primary' : ((snack.suc === -1)? 'error' : 'success')"
    v-model="snackbar"
    timeout=3000
    >
    {{ snack.msg }}
      <template v-slot:action="{ attrs }">
        <v-btn
        icon
        color="white"
        v-bind="attrs"
        @click="snackbar = false"
        >
          <v-icon>close</v-icon>
        </v-btn>
      </template>
    </v-snackbar>

    <v-footer
    class="footer"
    >
      <config-view
      :config="config"
      :clear="clear"
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
    config: null,
    clear: false,

    snackbar: false,
    snack: {
      suc: 0,
      msg: ''
    }
  }),

  methods: {
    sendFile(e){
      this.file = e;
    },
    sendConfig(e){
      this.config = e;
    },
    sendClear(e){
      this.clear = e;
      setTimeout(() => {
        this.clear = !e;
      }, 100);
    },
    getSnack(e){
      this.snack = e;
      this.snackbar = true;
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