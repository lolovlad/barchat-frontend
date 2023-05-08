<template>
 <div class="file__div">
   <label>
     <input
         type="file"
         @change="fileSelected"
         ref="inputFile"
     >
     <span>{{nameFile}}</span>
   </label>
 </div>
</template>

<script>
export default {
  props: {
    data: String,
  },
  emits: ['update:data'],
  data(){
    return {
      selectFile: null,
      nameFile: "Выберите файл"
    }
  },
  methods: {
    fileSelected(event){
      const reader = new FileReader()
      reader.addEventListener('load', (event) => {
        this.selectFile = event.target.result;
        this.$emit('update:data', this.selectFile)
      });
      if(event.target.files[0])
        this.nameFile = event.target.files[0].name
        reader.readAsText(event.target.files[0])
    },
    clearSelected(){
      this.$refs.inputFile.value = null;
      this.nameFile = "Выберите файл"
    }
  },
  name: "FileInput"
}
</script>

<style scoped>
input[type=file]{
  display: none;
}
label{
  width: 100%;
  height: 50px;
  background: #b50034;
  color: white;
  font-size: 20px;
  text-align: center;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>