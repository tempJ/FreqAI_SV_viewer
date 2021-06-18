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
    name: 'WaveChart',

    data: () => ({
      chartId: null,
      db: null,
      chart: null,
      series: [],
      legend: null,
      strokeTheme: [],
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

        this.legend = this.chart.addLegendBox(LegendBoxBuilders.VerticalLegendBox);

        // this.chart.onSeriesBackgroundMouseClick(this.getWavePoint);

        const xAxis = this.chart.getDefaultAxisX();
        xAxis.setChartInteractionPanByDrag(false);
        xAxis.setChartInteractionZoomByWheel(false);

        // const yAxis = this.chart.getDefaultAxisY();
        // yAxis.setChartInteractionFitByDrag(false);
        // yAxis.setChartInteractionZoomByDrag(false);
        // yAxis.setChartInteractionPanByDrag(false);
        // yAxis.setChartInteractionZoomByWheel(false);
        // yAxis.setMouseInteractions(false);

        // this.createSeries();
      },
      // createSeries(){
      //   this.strokeTheme.push(new SolidLine({
      //     fillStyle: new SolidFill({ color: colorSet[0] }),
      //     thickness: 1
      //   }));

      //   this.series = this.chart.addLineSeries().setStrokeStyle(this.strokeTheme[0]);
      // },
      createSeries(i){
        this.strokeTheme.push(new SolidLine({
          fillStyle: new SolidFill({ color: colorSet[i][0] }),
          thickness: 1
        }));

        const theme = this.strokeTheme;
        this.series.push(
          this.chart.addLineSeries()
          .setName(this.name[i])
          .setStrokeStyle(theme[theme.length - 1])
        );
      },
    },

    watch: {
      // 'yData': 'renderChart',
      data: {
        deep: true,

        handler(val){
          
          // this.series.add(this.data);

          const num = val.length;

          for(let i=0; i < num; i++){
            if(this.series[i] !== undefined){ this.series[i].clear(); }
            else{ this.createSeries(i); }

            this.series[i].add(val[i]);
            
          }

          this.legend.add(this.chart);
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
      
      // this.createSeries();
      // this.lightningChart = lightningChart();
      // this.chartXY = this.lightningChart.ChartXY({
      //   // chartContainer
      // });
      // this.series = this.chartXY.addLineSeries();
      // const tmp = this.genDataObj(this.xData, this.yData);
      // this.series.add(tmp);
    }
  }
</script>

<style scoped>
  .chart {
    height: 480px;
  }
</style>