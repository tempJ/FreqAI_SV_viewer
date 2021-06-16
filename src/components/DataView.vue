<template droppable="true">
    <v-container>
      <wave-chart
      :data="data2080"
      @wave="getWave"
      />

      <watch-chart
      :data="data5"
      />
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
      data2080: null,
      data5: null,
    }),

    methods: {
      getWave(e){

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
          console.log(val);
        }
      }
    },

    // computed: {
    // },

    created() {
      this.data2080 = this.genDataObj(xArr, new Array(size));
      this.data5 = [[], [], [], [], []];
    }
  }
</script>

<style scoped>
</style>