<template>
  <castom-header class="header"
                 @dateNow="dateNow"
                 @searchDate="searchDate"
                 @download="download"
                 @exportPNG="exportPNG"
                 @searchSeriesDate="searchSeriesDate"
  />
  <div class="main">
    <div class="select__zone">
      <select v-model="selectZone">
        <option v-for="z in zone" :key="z.val" :value="z.val">Зона {{z.text}}</option>
      </select>
      <select v-model="selectType">
        <option v-for="z in typeData" :key="z.val" :value="z.val">{{z.text}}</option>
      </select>
    </div>
    <div class="line" v-if="viewData">
      <CastomBar :chartdata="dataLineComp"
                 :options="optionsLine"
                 ref="lineChart"
      ></CastomBar>
    </div>
  </div>
</template>

<script>
import CastomBar from "@/components/UI/CastomBar";
import CastomHeader from "@/components/castomHeader";
import axios from "axios";

export default {
  name: "MainPage",
  components: {
    CastomHeader,
    CastomBar,
  },
  data(){
    return{
      seriesNow: null,
      optionsLine: {

        responsive: true,
        maintainAspectRatio: false
      },
      viewData: null,
      zone: [
        {val: 1, text: "1"},
        {val: 2, text: "2"},
        {val: 3, text: "3"},
      ],
      typeData: [
        {val: 1, text: "Температура"},
        {val: 2, text: "Вольтаж"}
      ],
      retypeData: {
        1: "Температура",
        2: "Вольтаж",
      },
      selectZone: 1,
      selectType: 1
    }
  },
  computed: {
    dataLineComp(){
      if(this.viewData != null){
        const labelsNow = []
        const dataNow = []

        for(let col of this.viewData){
          labelsNow.push(col.time)
          dataNow.push(col.data)
        }
        return {
          labels: labelsNow,
          datasets: [
            {
              label: this.retypeData[this.selectType],
              backgroundColor: '#f87979',
              data: dataNow
            }
          ]
        }
      }else{
        return {}
      }
    }
  },
  methods: {
    async getSeriesNow(){
      const date = new Date()
      const y = date.getUTCFullYear()
      const m = date.getUTCMonth() + 1
      const d = date.getUTCDay()
      const strDate = `${y}-${m}-${d}`
      const response = await axios.get(
          `http://${process.env.VUE_APP_HOST_SERVER}:${process.env.VUE_APP_PORT_SERVER}/v1/points/${this.selectZone}/${this.selectType}/${strDate}/all_series`

      )
      this.viewData = response.data
    },

    async dateNow(){
      clearInterval(this.seriesNow)
      await this.getSeriesNow()
      this.seriesNow = setInterval(this.getSeriesNow, 1000)
    },
    async searchDate(date){
      if(this.seriesNow != null)
        clearInterval(this.seriesNow)
      try {
        const response = await axios.get(
            `http://${process.env.VUE_APP_HOST_SERVER}:${process.env.VUE_APP_PORT_SERVER}/v1/points/${this.selectZone}/${this.selectType}/${date}/all_series`
        )
        this.viewData = response.data
      }catch (err){
        alert("Файла не существует")
      }
    },
    async download(date, startDate, endDate, type){
      try {
        let query = ""
        if(type !== "allFile"){
          date = `${startDate}_${endDate}`
          const start = startDate.split("-")
          const end = endDate.split("-")

          const startYear = parseInt(start[0])
          const startMou = parseInt(start[1])
          const startDay = parseInt(start[2])

          const endYear = parseInt(end[0])
          const endMou = parseInt(end[1])
          const endDay = parseInt(end[2])

          const startDays = startYear * 365 + startMou * 31 + startDay

          const endDays = endYear * 365 + endMou * 31 + endDay
          query = `http://${process.env.VUE_APP_HOST_SERVER}:${process.env.VUE_APP_PORT_SERVER}/v1/files/${this.selectZone}/${this.selectType}/download/${date}?type_series=${type}&start=${startDays}&end=${endDays}`
        }else{
          query = `http://${process.env.VUE_APP_HOST_SERVER}:${process.env.VUE_APP_PORT_SERVER}/v1/files/${this.selectZone}/${this.selectType}/download/${date}?type_series=${type}&start=0&end=0`
        }
        const response = await axios.get(
            query,
            {
              responseType: 'blob'
            },
        )
        const FILE = window.URL.createObjectURL(new Blob([response.data]));
        const docUrl = document.createElement('a');
        docUrl.href = FILE;
        docUrl.setAttribute('download', `${date}Z${this.selectZone}T${this.retypeData[this.selectType]}.csv`);
        document.body.appendChild(docUrl);
        docUrl.click();
      }catch (err){
        alert("Файла не существует")
      }
    },
    exportPNG(){
      this.$refs.lineChart.exportPNG()
    },
    async searchSeriesDate(startDate, endDate){
      const start = startDate.split("-")
      const end = endDate.split("-")

      const startYear = parseInt(start[0])
      const startMou = parseInt(start[1])
      const startDay = parseInt(start[2])

      const endYear = parseInt(end[0])
      const endMou = parseInt(end[1])
      const endDay = parseInt(end[2])

      const startDays = startYear * 365 + startMou * 31 + startDay

      const endDays = endYear * 365 + endMou * 31 + endDay
      clearInterval(this.seriesNow)
      try {
        const response = await axios.get(
            `http://${process.env.VUE_APP_HOST_SERVER}:${process.env.VUE_APP_PORT_SERVER}/v1/points/file/date/series?start_series=${startDays}&end_series=${endDays}`
        )
        this.viewData = response.data
      }catch (err){
        alert("Файла не существует")
      }
    }
  }
}
</script>

<style scoped>
.header{
  width: 100%;
  height: 60px;
  background: #b50034;
}
.line{
  width: auto;
  height: 600px;
  margin: auto;
  padding: 20px 20px;
}
.select__zone{
  height: 70px;
  width: 100%;
  background: burlywood;
  display: flex;
  gap: 20px;
  justify-content: center;
  align-items: center;
}
.select__zone > select{
  padding: 10px 20px;
  font-size: 15px;
  font-weight: bold;
}
</style>