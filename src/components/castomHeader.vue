<template>
  <div class="main__body">
    <CastomButton @click="dateNow">
      Отслеживать нынешнюю дату
    </CastomButton>
    <div class="search">
      <DateInput v-model="dateSearch"/>
      <CastomButton @click="searchDate">
        Найти
      </CastomButton>
    </div>
    <div class="search">
      <DateInput v-model="dateSearchStart"/>
      <DateInput v-model="dateSearchEnd"/>
      <CastomButton @click="searchSeriesDate">
        Найти
      </CastomButton>
    </div>
    <div class="bar__menu">
      <CastomButton @click="$emit('download', dateSearch, dateSearchStart, dateSearchEnd, typeFile)" v-if="typeFile">
        Скачать файл csv
      </CastomButton>
      <CastomButton @click="$emit('exportPNG')">
        Экспорт в формате png
      </CastomButton>
    </div>
  </div>
</template>

<script>
import CastomButton from "@/components/UI/CastomButton";
import DateInput from "@/components/UI/DateInput";
export default {
  name: "castomHeader",
  components: {DateInput, CastomButton},
  data(){
    return{
      dateSearch: null,
      dateSearchStart: null,
      dateSearchEnd: null,
      typeFile: null
    }
  },
  methods: {
    dateNow(){
      const date = new Date()
      this.dateSearch = date.toISOString().split("T")[0]
      this.typeFile = "allFile"
      this.$emit('dateNow')
    },
    searchDate(){
      this.typeFile = "allFile"
      this.$emit('searchDate', this.dateSearch)
    },
    searchSeriesDate(){
      this.typeFile = "dateSeries"
      this.$emit('searchSeriesDate', this.dateSearchStart, this.dateSearchEnd)
    }

  }
}
</script>

<style scoped>
.main__body{
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px 100px;
  gap: 70px;
}
.search{
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 5px;
}
.bar__menu{
  display: flex;
  justify-items: center;
  align-items: center;
  gap: 5px;
}
</style>