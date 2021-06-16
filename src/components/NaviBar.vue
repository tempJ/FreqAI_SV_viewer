<template>
  <v-container>
    <v-card
    tile
    >
      <v-navigation-drawer
      app
      permanent
      width="280px"
      v-model="drawer"
      :color="color[theme]"
      :dark="(theme === 2)? true : false"
      >
        <v-list-item>
          <v-spacer></v-spacer>
          <v-btn
          small
          icon
          @click="theme = (theme+1)%3"
          >
            <v-icon>lightbulb</v-icon>
          </v-btn>
        </v-list-item>

        <v-divider></v-divider>

        <v-list
        dense
        >
          <v-list-item>
            <v-list-item-icon>
              <v-icon>attach_file</v-icon>
            </v-list-item-icon>
            <v-list-item-title>{{ name }}</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-icon>
              <v-icon>format_list_numbered</v-icon>
            </v-list-item-icon>
            <v-list-item-title>{{ format }}</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-icon>
              <v-icon>memory</v-icon>
            </v-list-item-icon>
            <v-list-item-title>{{ hwType }}</v-list-item-title>
          </v-list-item>
        </v-list>

        <template v-slot:append>
          <v-list
          dense
          >
            <v-list-item
            link
            @click="loadCsv"
            >
              <v-list-item-icon>
                <v-icon>file_upload</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Load .csv</v-list-item-title>
            </v-list-item>
          </v-list>
        </template>
      </v-navigation-drawer>
    </v-card>
  </v-container>
</template>

<script>
import fs from 'fs';
import { remote } from 'electron';

const dialog = remote.dialog;

  export default {
    props: {
      // save: Object
    },
    name: 'NaviBar',

    data: () => ({
      drawer: true,
      mini: true,
      theme: 1,
      color: ['white', '#EEEEEE', 'normal'],
      isFile: false,
      name: '-',
      format: '-',
      hwType: '-',
      file: [],
      // err: null,
      send: new Object(),
      // config: new Object()

      // config: new Object(),
      // recipe: {
      //   path: 'C:/Users/Administrator/Documents/FreqAi SV/csv',
      //   interval: 100,
      //   integration: 25,
      //   sampling: 1000,
      //   auto: {
      //     start: 0,
      //     end: 0,
      //     threshold: 10000,
      //     use: true
      //   },
      //   wave: [
      //     { view: false, start: 0, end: 0 },
      //     { view: false, start: 0, end: 0 },
      //     { view: false, start: 0, end: 0 },
      //     { view: false, start: 0, end: 0 },
      //     { view: false, start: 0, end: 0 },
      //   ],
      // }
    }),

    methods: {
      async loadCsv(){
        dialog.showOpenDialog({
          title: 'Open Csv',
          // defaultPath: this.recipe.path,
          filters: [
            { 'name': 'Csv Files(*.csv)', 'extensions': ['csv'] },
            { 'name': 'All(*.*)', 'extensions': ['*'] },
          ],
          properties: ['openFile']
        })
          .then((res) => {
            const file = [];
            const path = res.filePaths[0];

            fs.readFile(path, 'utf-8', (err, data) => {
              if(err !== null){ return -1; }

              for(const el of data.split('\r\n')){
                file.push(el.split(','));

                if(!this.isFile){ continue; }

                if(file.length === 1 && this.format !== file[0][0].split(':').pop()){
                  // 첫번째줄 검사
                  return -1;
                }
                if(file.length === 2 && this.hwType !== file[1][0].split(':').pop()){
                  // 두번째줄 검사
                  return -1;
                }
              }

              if(!this.isValidForm()){ return -1; }

              this.format = file[0][0].split(':').pop();
              this.hwType = file[1][0].split(':').pop();

              this.send.integration = parseInt(file[3][0].split(':').pop());
              this.send.interval = parseInt(file[3][1].split(':').pop());
              this.send.sampling = parseInt(file[3][2].split(':').pop());
              // console.log(file)

              const wave = [];
              for(const i of file[4].keys()){
                if(i > 2){
                  const range = file[4][i].split('~');
                  const pair = {
                      'view': true,
                      'start': parseInt(range[0]),
                      'end': parseInt(range[range.length - 1])
                    };
                  wave.push(new Object(pair));
                }
              }
              this.send.wave = wave;

              const watch = [];
              const slice = file.slice(5, file.length - 1);

              slice.forEach((line) => {
                const tmp = line.map((el, i) => {
                  if(i === 0){ return el; }
                  else{ return parseInt(el); }
                });
                watch.push(tmp);
              });
              this.send.watch = watch;

              this.name = path.split('\\').pop();

              this.$emit("file", this.send);

              // console.log(this.send)
            });
          })
          .catch();
      },

      // setConfig(){
      //   Object.assign(this.config, this.recipe);
      //   this.$emit("config", this.config);
      // },

      isValidForm(){
        // 파일 유효성 검사
        return true;
      }
    },
  }
</script>

<style scoped>
.input {
  height: 30px;
  font-size: 12px;
}
::v-deep .checkbox .v-label{
  font-size: 12px;
}
</style>