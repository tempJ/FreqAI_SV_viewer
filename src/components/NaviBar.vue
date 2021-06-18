<template>
  <!-- <v-container> -->
    <v-card
    tile
    :color="color[theme]"
    :dark="(theme === 2)? true : false"
    >

    <v-btn
    icon
    plain
    width="20px"
    >
      <v-icon>drag_indicator</v-icon>
    </v-btn>

    <v-btn
    icon
    @click="theme = (theme+1)%3"
    >
      <v-icon>lightbulb</v-icon>
    </v-btn>

    <v-divider vertical></v-divider>

    <v-btn
    icon
    :disabled="!addDisabled"
    @click="loadCsv(false)"
    >
      <v-icon color="primary">file_upload</v-icon>
    </v-btn>

    <v-btn
    icon
    :disabled="addDisabled"
    @click="loadCsv(true)"
    >
      <v-icon
      color="info"
      :disabled="addDisabled"
      >add</v-icon>
    </v-btn>

    <v-btn
    icon
    @click="addDisabled = true"
    >
      <v-icon color="error">delete</v-icon>
    </v-btn>

    <v-divider></v-divider>
    <v-btn
    icon
    plain
    width="20px"
    >
      <v-icon>drag_indicator</v-icon>
    </v-btn>

    <v-divider vertical></v-divider>

    <!-- <v-icon>attach_file</v-icon>{{ name }}

    <v-divider vertical></v-divider> -->

    <v-icon>format_list_numbered</v-icon>{{ format }}

    <v-divider vertical></v-divider>

    <v-icon>memory</v-icon>{{ hwType }}

    <!-- <v-spacer></v-spacer> -->

    
      <!-- <v-navigation-drawer
      app
      permanent
      width="280px"
      v-model="drawer"
      :color="color[theme]"
      :dark="(theme === 2)? true : false"
      >
      <v-navigation-drawer
      app
      permanent
      width="280px"
      v-model="drawer"
      :color="color[theme]"
      :dark="(theme === 2)? true : false"
      > -->
        <!-- <v-list-item>
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
          <v-list-item
          link
          :disabled="!addDisabled"
          @click="loadCsv(false)"
          >
            <v-list-item-icon>
              <v-icon color="primary">file_upload</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Load .csv</v-list-item-title>
          </v-list-item>

          <v-list-item
          link
          :disabled="addDisabled"
          @click="loadCsv(true)"
          >
            <v-list-item-icon>
              <v-icon
              color="info"
              :disabled="addDisabled"
              >add</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Add .csv</v-list-item-title>
          </v-list-item>

          <v-list-item
          link
          @click="addDisabled = true"
          >
            <v-list-item-icon>
              <v-icon color="error">delete</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Clear</v-list-item-title>
          </v-list-item>
        </v-list>

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
        </v-list> -->

        <!-- <template v-slot:append>
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
        </template> -->
      <!-- </v-navigation-drawer>
      </v-navigation-drawer> -->
    </v-card>
  <!-- </v-container> -->
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
      theme: 0,
      color: ['white', '#EEEEEE', 'normal'],
      isFile: false,
      name: '-',
      format: '-',
      hwType: '-',
      addDisabled: true,
      // file: [],
      // err: null,
      // send: new Object(),
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
      async loadCsv(isFile = false){
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
            // const file = [];
            const path = res.filePaths[0];

            fs.readFile(path, 'utf-8', (err, data) => {
              if(err !== null){ return -1; }

              const file = data.split('\r\n').map((el) => { return el.split(','); });

              // for(const el of data.split('\r\n')){
              //   file.push(el.split(','));

              //   if(!this.isFile){ continue; }

              //   if(file.length === 1 && this.format !== file[0][0].split(':').pop()){
              //     console.log('The file format is different.');
              //     return -1;
              //   }
              //   if(file.length === 2 && this.hwType !== file[1][0].split(':').pop()){
              //     console.log('The HW type is different.');
              //     return -1;
              //   }
              // }

              const prefix = file.splice(0, 5);
              // console.log(file)
              // const p = file;
              // console.log(prefix)
              // console.log(file)
              console.log(isFile)

              if(!this.isValidForm(prefix, file, isFile)){
                console.log('CSV file format is invalid.');
                return -1;
              }

              this.sendFile(path.split('\\').pop(), prefix, file, isFile);
              if(!isFile){ this.addDisabled = false; }
            });
          })
          .catch();
      },
      sendFile(name, prefix, data, isFile){
        if(!isFile){
          this.name = name;
          this.format = prefix[0][0].split(':')[1];
          this.hwType = prefix[1][0].split(':')[1];
        }

        const send = new Object();

        send.name = name;
        send.integration = parseInt(prefix[3][0].split(':')[1]);
        send.interval = parseInt(prefix[3][1].split(':')[1]);
        send.sampling = parseInt(prefix[3][2].split(':')[1]);
        
        send.wave = prefix[4].slice(3).map((el) => {
          const range = el.split('~');
          return {
            'view': true,
            'start': parseInt(range[0]),
            'end': parseInt(range[range.length - 1])
          };
        });

        const len = send.wave.length;
        send.watch = data.map((el) => {
          return {
            'serial': [parseFloat(el[1]), parseFloat(el[2])],
            'wave': el.slice(3, len + 3).map(ret => parseFloat(ret)),
            'data': el.slice(len + 3).map(ret => parseFloat(ret))
          };
        });
        // console.log(data)
        // const watch = [];
        // const len = this.send.wave.length;
        // data.forEach((line) => {
        //   watch.push({
        //     'serial': [parseInt(line[1]), parseInt(line[2])],
        //     'wave': line.splice(3, len + 3),
        //     'data': line.splice(len + 3)
        //   });
        // })
        // this.send.watch = watch;
        // this.send.watch = data.map((el) => {
        //   console.log(el)
        //   return line
        // })
        // .map((line) => {
        //   const item = line.split(',');
        //   const len = this.send.wave.length;
        //   return {
        //     'time': item[0],
        //     'serial': [item[1], item[2]],
        //     'wave': item.splice(3, len + 3),
        //     'data': item.splice(len + 3)
        //   };
        //   item.map((el) => {
        //     return {
        //       'time': el[0],
        //       'serial': []
        //     }
        //     const num = parseInt(el);
        //     return isNaN(num)? el : num;
        //     // if(isNaN(num)){ return el; }
        //     // else
        //   });
        // });
        
        // const watch = [];
        // const slice = file.slice(5, file.length - 1);

        // slice.forEach((line) => {
        //   const tmp = line.map((el, i) => {
        //     if(i === 0){ return el; }
        //     else{ return parseInt(el); }
        //   });
        //   watch.push(tmp);
        // });
        // this.send.watch = watch;
        console.log(send)
        
        this.$emit("file", send);
        this.$emit("config", send);
        // this.isFile = true;
      },

      // setConfig(){
      //   Object.assign(this.config, this.recipe);
      //   this.$emit("config", this.config);
      // },

      isValidForm(prefix, data, isFile){
        if(prefix.length !== 5 || data.length < 1){ return false; }

        const form = prefix.slice(0, 2);
        const config = prefix.slice(3);
        let watch;
        
        form.forEach((line) => {
          // console.log(line[])
          const tmp = line[0].split(":");

          if(tmp.length !== 2){ return false; }

          // if(i == 0){
          //   if(tmp[1] !== '1'){ return false; }
          // }
          // else if(i == 1){
          //   if(tmp[1] !== 'SPdbUSBm'){ return false; }
          // }
          // else{}
        });

        config.forEach((line, i) => {
          const tmp = line[0].split(",");

          if(i == 0){
            if(tmp.length !== 3){ return false; }
            if(tmp[0].split(":").length !== 2){ return false; }
            if(tmp[1].split(":").length !== 2){ return false; }
            if(tmp[2].split(":").length !== 2){ return false; }
          }
          else if(i == 1){
            if(tmp.length < 3){ return false; }
            watch = tmp.length - 3;
          }
          else{}
        });

        data.forEach((line) => {
          const tmp = line[0].split(",");

          if(tmp.length !== watch + 3){ return false; }
        });

        if(isFile){
          if(form[0][0].split(":")[1] !== this.format){ return false; }
          if(form[1][0].split(":")[1] !== this.hwType){ return false; }
        }
        
        // const date = prefix[2].split(" ")[1].split(":")[1];
        // const time = prefix[2].split(" ")[2].split("(ms)")[0];

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