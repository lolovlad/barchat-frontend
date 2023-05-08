<template>
  <Line :data="$props.chartdata" :options="$props.options" ref="lineChart"></Line>
</template>
<script>
import { Line } from 'vue-chartjs'
import { Chart as ChartJS, CategoryScale, LinearScale, PointElement, LineElement, Title, Tooltip, Legend} from 'chart.js'
ChartJS.register(Title, Tooltip, Legend, PointElement, CategoryScale, LinearScale, LineElement)

export default {
  name: "CastomBar",
  components: {
    Line
  },
  data() {
    return {
      chartData: this.$props.chartdata,
      chartOptions: this.$props.options
    }
  },
  props: ['chartdata', 'options'],
  methods: {
    exportPNG(){
      const link = document.createElement('a')
      link.href = this.$refs.lineChart.chart.toBase64Image()
      link.download = 'Диограмма.jpg';
      link.click();
    }
  }
}
</script>

<style scoped>

</style>