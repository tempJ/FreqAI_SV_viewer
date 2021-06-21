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
    @click="clearAll"
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
const snack = {
  'clear': { suc: 0, msg: 'Clear all files.' },
  'load': {
    'success': { suc: 1, msg: 'Success load file.' },
    'dont': { suc: 0, msg: 'No file loaded.' },
    'filemax': { suc: 0, msg: 'The maximum number of files has been exceeded.' },
    'invalid': { suc: -1, msg: 'Invalid file format.' },
    'format': { suc: -1, msg: 'Different format type.' },
    'hwtype': { suc: -1, msg: 'Different HWType.' },
  }
}

  export default {
    name: 'NaviBar',

    data: () => ({
      // drawer: true,
      // mini: true,
      theme: 0,
      color: ['white', '#EEEEEE', 'normal'],
      addDisabled: true,

      isFile: false,

      // name: '-',
      format: '-',
      hwType: '-',
    }),

    methods: {
      async loadCsv(isFile = false){
        dialog.showOpenDialog({
          // title: 'Open Csv',
          // defaultPath: this.recipe.path,
          filters: [
            { 'name': 'Csv Files(*.csv)', 'extensions': ['csv'] },
            { 'name': 'All(*.*)', 'extensions': ['*'] },
          ],
          properties: ['openFile']
        })
          .then((res) => {
            if(res.filePaths.length < 1){
              this.$emit("snack", snack.load.dont);
              return -1;
            }
            const path = res.filePaths[0];

            fs.readFile(path, 'utf-8', (err, data) => {
              if(err !== null){
                this.$emit("snack", { suc: -1, msg: err });
                return -1;
              }

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

              if(!this.isValidForm(prefix, file)){
                this.$emit("snack", snack.load.invalid);
                return -1;
              }

              if(isFile){
                if(prefix[0][0].split(":")[1] !== this.format){
                  this.$emit("snack", snack.load.format);
                  return false;
                }
                if(prefix[1][0].split(":")[1] !== this.hwType){
                  this.$emit("snack", snack.load.hwtype);
                  return false;
                }
              }
              else{
                this.addDisabled = false;
              }

              const name = path.split('\\').pop().split('.');

              this.sendFile(name.slice(0, name.length - 1).join('.'), prefix, file, isFile);
              this.$emit("snack", snack.load.success);
              // if(!isFile){ this.addDisabled = false; }
              
            });
          })
          .catch();
      },

      clearAll(){
        this.isFile = false;

        this.format = '-';
        this.hwType = '-';

        this.addDisabled = true;

        this.$emit("clear", true);
        this.$emit("snack", snack.clear);
      },

      sendFile(name, prefix, data, isFile){
        if(!isFile){
          // this.name = (name === '')? 'no name' : name;
          this.format = prefix[0][0].split(':')[1];
          this.hwType = prefix[1][0].split(':')[1];
        }

        const send = new Object();

        send.name = ((name==='') || (name===null))? 'no name' : name;
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

        console.log(send);
        this.$emit("file", send);
        this.$emit("config", send);
      },

      isValidProperty(property){
        const split = property.split(':');

        if(split.length !== 2){ return false; }
        if(split.find((val) => val === '') !== undefined){ return false; }
        return true;
      },

      isValidForm(prefix, data){
        // console.log(1);
        if(prefix.length !== 5 || data.length < 1){ return false; }
        // console.log(2);

        const form = prefix.slice(0, 2);
        const config = prefix.slice(3);
        let watch;

        for(const line of form){
          if(!this.isValidProperty(line[0])){ return false; }
          // console.log(3);
        }

        for(const line of config){
          const idx = config.indexOf(line);

          if(idx === 0){
            if(line.length !== 3){ return false; }
            // console.log(4, idx);
            for(const el of line){
              if(!this.isValidProperty(el)){ return false; }
              // console.log(5, el);
            }
          }
          else if(idx === 1){
            if(line.length < 4){ return false; }
            // console.log(6, idx);
            if(line.find((val) => val === '') !== undefined){ return false; }
            // console.log(7, idx);
            watch = line.length - 3;
          }
          else{}
        }

        for(const line of data){
          // console.log(8, line.length);
          if(line.length !== (watch+2083)){ return false; }
          
        }

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