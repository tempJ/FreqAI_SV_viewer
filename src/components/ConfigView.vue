<template>
  <!-- <v-container> -->
    <v-row>
      <v-col
      class="config"
      v-for="(f, idx) in file"
      :key="idx"
      >
        <v-card
        tile
        >
          <v-simple-table>
            <template v-slot:default>
              <tbody>
                <tr>
                  <td>File Name</td>
                  <td>{{ f.name }}</td>
                </tr>
                <tr>
                  <td>Interval Time</td>
                  <td>{{ f.interval }}</td>
                </tr>
                <tr>
                  <td>Integration Time</td>
                  <td>{{ f.integration }}</td>
                </tr>
                <tr>
                  <td>Sampling Time</td>
                  <td>{{ f.sampling }}</td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-card>
      </v-col>
        
    </v-row>
  <!-- </v-container> -->
</template>

<script>
  export default {
    props: {
      config: Object
    },
    name: 'ConfigView',

    data: () => ({
      file: []
    }),

    methods: {

    },
    watch: {
      config: {
        deep: true,

        handler(val){
          console.log(val);
          this.file.push({
            name: val.name,
            interval: val.interval,
            integration: val.integration,
            sampling: val.sampling,
          });
        }
      }
    }
  }
</script>

<style scoped>
.config {
  padding: 0px;
  margin: 0px;
}
</style>