<template>
  <v-container>
    <wave-chart
    :name="name"
    :data="data2080"
    :clear="clear"
    />

    <watch-chart
    :name="wave"
    :data="data5"
    :clear="clear"
    @focus="getFocus"
    />
  </v-container>
</template>

<script>
// component
import WaveChart from './WaveChart';
import WatchChart from './WatchChart';

const snack = {
  'nowave': { suc: -1, msg: 'Select the wrong point.' }
}

  export default {
    components: {
      WaveChart, WatchChart,
    },

    props: {
      file: Object,
      clear: Boolean,
    },
    
    name: 'DataView',

    data: () => ({
      fileArr: [],
      wL: [],
      data2080: [],
      data5: [],
      name: [],
      wave: [],

      isClear: false,
    }),

    methods: {
      getFocus(e){
        if(e < 0){
          this.$emit("snack", snack.nowave);
          return -1;
        }

        const files = this.fileArr;
        this.name = [];
        this.data2080 = [];
        
        files.forEach((f, i) => {
          const idx = this.time2Idx(e, i);

          if(f.watch.length <= idx){
            this.name.push(f.name);
            this.data2080.push([]);
          }
          else if(f.watch[idx].data !== undefined){
            this.name.push('#file' + (i + 1));
            this.data2080.push(this.genDataObj(this.wL, f.watch[idx].data));
          }
          else{
            this.name.push(f.name);
            this.data2080.push([]);
          }
        });
      },

      time2Idx(time, file){
        return Math.round(time / this.fileArr[file].interval);
      },

      // genDataObj(xArr, yArr){
      //   const tmp = [];
      //   const len = xArr.length;
      //   for(let i=0; i<len; i++){
      //     const item = new Object();
      //     item.x = xArr[i];
      //     item.y = yArr[i];
      //     tmp.push(item);
      //   }
      //   return tmp;
      // },

      genDataObj(xArr, yArr){
        return xArr.map((el, i) => { return { 'x': el, 'y': yArr[i] }; });
      },
    },

    watch: {
      file: {
        deep: true,

        handler(val){
          this.fileArr.push(val);
          
          const wave = val.watch.map(el => el.wave),
            len = val.watch.length,
            num = val.wave.length,
            intv = val.interval;

          // this.wave2080 = val.table;

          let serise = [];
          this.wave = [];

          for(let i=0; i<num; i++){
            const start = val.wave[i].start,
              end = val.wave[i].end;
            const range = (start === end)?
              '' + start.toFixed(2) : start.toFixed(2) + '~' + end.toFixed(2);
            this.wave.push(range);
            serise.push(new Array(len));
          }

          wave.forEach((line, i) => {
            line.forEach((el, j) => {
              const t = i * intv;
              serise[j][i] = { 'x': t, 'y': el };
            });
          });
          
          this.data5 = serise;
        }
      },
      clear(val){
        console.log(val);
        if(val){
          this.fileArr = [];
          this.data2080 = [];
          this.data5 = [];
          this.name = [];
          this.wave = [];
        }
      },
    },

    created() {
      this.wL = new Array(2048).fill(0).map((el, i) => { return i; })
      // console.log(this.wave2080);
    },
    // mounted() {}
  }
</script>

<style scoped>
</style>