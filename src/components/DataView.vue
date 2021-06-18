<template>
  <v-container>
    <wave-chart
    :name="name"
    :data="data2080"
    />

    <watch-chart
    :name="wave"
    :data="data5"
    @focus="getFocus"
    />
    <!-- <div>{{ file }} </div> -->
  </v-container>
</template>

<script>
// component
import WaveChart from './WaveChart';
import WatchChart from './WatchChart';

// constant
const size = 2080;
const xArr = genSeqArr();

function genSeqArr(){
  const tmp = [];
  for(let i=0; i<size; i++){
    tmp.push(i);
  }
  return tmp;
}
  export default {
    components: {
      WaveChart, WatchChart,
    },

    props: {
      file: Object,
    },
    
    name: 'DataView',

    data: () => ({
      fileArr: [],
      data2080: [],
      data5: [],
      name: [],
      wave: []
    }),

    methods: {
      getFocus(e){
        // console.log(e)
        if(e < 0){ return -1; }
        // const idx = this.time2Idx(e, 0);
        // console.log(idx)

        // this.data2080 = this.genDataObj(xArr, this.fileArr[0].watch[idx].data);

        const files = this.fileArr;
        this.name = [];
        this.data2080 = [];
        
        files.forEach((f, i) => {
          const idx = this.time2Idx(e, i);
          // console.log(idx, f.watch[idx].data)

          if(f.watch[idx].data !== undefined){
            this.data2080.push(this.genDataObj(xArr, f.watch[idx].data));
            this.name.push(f.name);
            // console.log(this.data2080)
          }
        });
        // console.log(this.fileArr[0].watch[idx])
        // console.log(y)
      },

      time2Idx(time, file){
        return Math.round(time / this.fileArr[file].interval);
      },

      genDataObj(xArr, yArr){
        const tmp = [];
        const len = xArr.length;
        for(let i=0; i<len; i++){
          const item = new Object();
          item.x = xArr[i];
          item.y = yArr[i];
          tmp.push(item);
        }
        return tmp;
      },
    },

    watch: {
      file: {
        deep: true,

        handler(val){
          // console.log(val)
          // this.name.push(val.name);
          this.fileArr.push(val);
          
          const wave = val.watch.map(el => el.wave),
            len = val.watch.length,
            num = val.wave.length,
            intv = val.interval;
          // let start = -intv;
          // console.log(len, num)

          let serise = [];
          this.wave = [];
          // new Array(num);
          for(let i=0; i<num; i++){
            // console.log('s')
            const range = (val.wave[i].start === val.wave[i].end)?
              '' + val.wave[i].start : val.wave[i].start + '~' + val.wave[i].end;
            this.wave.push(range);
            serise.push(new Array(len));
          }
          // serise[0][0] = 1
          // serise[0].push(1)
          // serise[0].push(1)
          // serise.forEach((el) => { el = []; })
          // console.log(serise[0])
          // return;
          wave.forEach((line, i) => {
            // start += intv;
            // const tmp = []
            line.forEach((el, j) => {
              const t = i * intv;

              // this.wave.push();
              serise[j][i] = { 'x': t, 'y': el };
            });
            // line.forEach((el) => {
            //   console.log(el)
            // })
            // for(let i=0; i < )
            // line.for((el) =>{
            //   start += intv;
            //   return {
            //     'x': start,
            //     'y': el
            //   };
            // }).forEach((obj, i) => {

            //   // if(this.data5[0].length === 0){ this.data5}
            //   serise.push(obj);
            // });
          });
          // console.log(serise)
          
          this.data5 = serise;
          // console.log(serise)
        }
      },
      // 'file.watch': {
      //   deep: true,

      //   handler(val){
      //     console.log(val);
      //     let start = -1;
      //     const wave = val.map(el => el.wave);

      //     wave.forEach((line) => {
      //       start++;
      //       line.map((el) =>{
      //         return {
      //           'x': start,
      //           'y': el
      //         };
      //       }).forEach((obj, i) => {
      //         // if(this.data5[0].length === 0){ this.data5}
      //         this.data5[i].push(obj);
      //       });
      //     });
      //   }
      // }
    },

    computed: {
      // interval(){
      //   console.log(this.file.interval)
      //   return this.file.interval;
      // },
      // integration(){
      //   console.log(this.file.integration)
      //   return this.file.integration;
      // },
      // integration(){
      //   return this.file.sampling;
      // }
    },

    created() {
      // this.data2080 = this.genDataObj(xArr, new Array(size));
      // this.data5 = [
      //   [{ 'x': 0, 'y': null }],
      //   [{ 'x': 0, 'y': null }],
      //   [{ 'x': null, 'y': null }],
      //   [{ 'x': null, 'y': null }],
      //   [{ 'x': null, 'y': null }]
      // ];
      // this.data5 = [
      //   [], [], [], [], [],
      //   [], [], [], [], [],
      //   [], [], [], [], [],
      //   [], [], [], [], [],
      //   [], [], [], [], [],
      // ];
    }
  }
</script>

<style scoped>
</style>