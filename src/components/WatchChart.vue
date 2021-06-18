<template>
  <!-- <v-container> -->
    <v-card
    flat
    tile
    >
      <div class="chart" :id="chartId"></div>
    </v-card>
  <!-- </v-container> -->
</template>

<script>
import {
  lightningChart,
  LegendBoxBuilders,
  // FontSettings,
  SolidLine, SolidFill, ColorRGBA, Themes
} from '@arction/lcjs';

// const size = 2048;
const colorSet = [
  [ // T-1A
    ColorRGBA(255, 194, 0),
    ColorRGBA(255, 115, 0),
    ColorRGBA(25, 115, 255),
    ColorRGBA(0, 56, 255),
    ColorRGBA(37, 22, 15),
  ],
  [ // T-4B
    ColorRGBA(77, 88, 213),
    ColorRGBA(115, 117, 230),
    ColorRGBA(156, 156, 255),
    ColorRGBA(214, 212, 191),
    ColorRGBA(7, 9, 12),
  ],
  [ // M-2A
    ColorRGBA(255, 255, 191),
    ColorRGBA(153, 222, 255),
    ColorRGBA(41, 74, 235),
    ColorRGBA(22, 10, 145),
    ColorRGBA(255, 120, 145),
  ],
  [ // M-4B
    ColorRGBA(0, 15, 201),
    ColorRGBA(36, 110, 255),
    ColorRGBA(66, 194, 255),
    ColorRGBA(102, 255, 179),
    ColorRGBA(0, 12, 72),
  ],
  [ // M-10C
    ColorRGBA(0, 0, 131),
    ColorRGBA(48, 64, 194),
    ColorRGBA(207, 89, 186),
    ColorRGBA(232, 255, 191),
    ColorRGBA(4, 4, 35),
  ]
];

  export default {
    props: {
      name: Array,
      data: Array,
    },
    name: 'WatchChart',

    data: () => ({
      chartId: null,
      db: null,
      chart: null,
      series: [],
      legend: null,
      strokeTheme: [],
      fileNum: 0
    }),

    methods: {
      resetChartID(){
        this.chartId = Math.trunc(Math.random() * 1000000);
      },
      createDb(){
        this.db = lightningChart().Dashboard({
          container: `${this.chartId}`,
          theme: Themes.light,
          numberOfColumns: 1,
          numberOfRows: 1,
        });

        this.createChart();
      },
      createChart(){
        this.chart = this.db.createChartXY({
          columnIndex: 0,
          rowIndex: 0,
        })
          .disableAnimations(false)
          .setTitle('');

        this.chart.onSeriesBackgroundMouseClick(this.getWavePoint);
        this.legend = this.chart.addLegendBox();
        // this.legend = this.chart.addLegendBox(LegendBoxBuilders.VerticalLegendBox,
        //   { x: this.chart.getDefaultAxisX(), y: this.chart.getDefaultAxisY() } );

        const xAxis = this.chart.getDefaultAxisX(); 
        xAxis.setChartInteractionPanByDrag(false);
        xAxis.setChartInteractionZoomByWheel(false);

        // const yAxis = this.chart.getDefaultAxisY();
        // yAxis.setChartInteractionFitByDrag(false);
        // yAxis.setChartInteractionZoomByDrag(false);
        // yAxis.setChartInteractionPanByDrag(false);
        // yAxis.setChartInteractionZoomByWheel(false);
        // yAxis.setMouseInteractions(false);

        // this.createSeries(0);
      },
      createSeries(i){
        // console.log(i)
        this.strokeTheme.push(new SolidLine({
          fillStyle: new SolidFill({ color: colorSet[this.fileNum][i] }),
          thickness: 1
        }));

        const theme = this.strokeTheme;
        this.series.push(
          this.chart.addLineSeries()
          .setName(this.name[i])
          .setStrokeStyle(theme[theme.length - 1])
        );

        // this.legend.add(this.chart);
        // this.legend = this.chart.addLegendBox(LegendBoxBuilders.VerticalLegendBox);
        // this.legend = this.chart.addLegendBox(LegendBoxBuilders.VerticalLegendBox,
        //   { x: this.chart.getDefaultAxisX(), y: this.chart.getDefaultAxisY() } );
      },

      getWavePoint(obj, e){
        const cursor = obj.engine.clientLocation2Engine(e.clientX, e.clientY);
        const near = obj.solveNearest(cursor);
        if(near !== undefined){
          const onScale = near.location;
          this.$emit("focus", Math.round(onScale.x));
        }
        else{ this.$emit("focus", -1); }
      },
    },

    watch: {
      // name: {
      //   deep: true,

      //   handler(val){
      //     console.log(val)
      //   }
      // },
      data: {
        deep: true,

        handler(val){
          if(this.fileNum === 5){ return -1; }
          // console.log(val)
          // const sNum = this.num;
          const num = val.length;
          // console.log(num, sNum)
          for(let i=0; i < num; i++){
            this.createSeries(i);
            // console.log(this.series)
            this.series[this.series.length - 1].add(val[i]);
            this.legend.add(this.series[this.series.length - 1]);
          }

          // this.num += num;
          // this.legend = []
          // this.legend = this.chart.addLegendBox();
          // console.log(this.chart)
          // this.legend.add(this.series[]);
          this.fileNum++;
          
          // console.log(this.series)
          // this.series[this.num].clear();

          // this.series[this.num].add(val[this.num]);
          // this.num++;
          // this.data.forEach((el, i) => {
          //   this.series[i].clear();
          //   this.series[i].add(el);
          // })
          // for(let i=0; i<5; i++){
          //   if(this.data[i].length < 1){ continue; }

          //   this.series[i].clear();
          //   this.series[i].add(this.data[i]);
          // }
        }
      }
    },

    computed: {
    },

    beforeMount(){
      this.resetChartID();
    },

    mounted() {
      this.createDb();
    }
  }
</script>

<style scoped>
  .chart {
    height: 480px;
  }
</style>