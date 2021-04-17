<template>
<section class="inputFile" :class="{'inputFile--errorType' : errorType, 'inputFile--complite': complite}">
  <div class="inputFile__block">
    <input class="inputFile__in" @change="fileInput" @drop="fileInput" :id="id" :placeholder="placeholder" :name="name" type="file">
    <p class="inputFile__text">{{textMessange}}</p>
  </div>
</section>
</template>

<script>
import XLSX from 'xlsx';

export default {
  name: 'InputFile',
  props: {
    id: {
      type: String,
      default: ''
    },
    sizeFile: {
      type: Number,
      default: 10*1024*1024
    },
    placeholder: {
      type: String,
      default: ''
    },
    name: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      textMessange: "Перетажите сюда фаил\nили нажмите что бы выбрать",
      errorType: false,
      complite: false,
      fileXl: null
    }
  },
  methods: {
    fileInput(event){
      let files
      if(event.type == "drop"){
        event.stopPropagation(); 
        event.preventDefault();
        files = event.dataTransfer.files;
      }else if(event.type == "change"){
        files = event.target.files;
      }
      if(files[0].name.slice(files[0].name.lastIndexOf('.')) == ".xlc" || files[0].name.slice(files[0].name.lastIndexOf('.')) == ".xlsx"){
        if(files[0].size <= this.sizeFile){
          let reader = new FileReader();
          reader.readAsArrayBuffer(files[0]);
          reader.onload = () => {
            let data = new Uint8Array(reader.result)
            this.fileXl = XLSX.read(data, {type: 'array'});
            let url = "https://193.243.158.230:4500/api/import";
            let req = new XMLHttpRequest();
            req.open("POST", url, true);
            req.setRequestHeader('Authorization', 'test-task');
            let body = {
              resultArray: this.fileXl
            };
            req.send(JSON.stringify(body));
            req.onload = () => {
              if(req.status == 200) {
                this.$parent.fileXl = this.fileXl;
              } else {
                console.log(req);
              }
            };
            this.textMessange = `Файл - ${files[0].name}`;
            this.complite = true;
          };
        }else{
          let textMes = '';
          if(this.sizeFile / 1024 < 1000){
            textMes = `${this.sizeFile / 1024}Кб`
          } else if(this.sizeFile / (1024*1024) < 1000){
            textMes = `${this.sizeFile / (1024*1024)}Мб`
          }
          this.textMessange = `Допустимый размер файла ${textMes}, выберете другой файл`
        }
      }else{
        this.textMessange = `Недопустимый тип файла ${files[0].name.slice(files[0].name.lastIndexOf('.'))}, выберете другой файл`;
        this.errorType = true;
      }
    }
  },
}
</script>

<style lang="scss">
.inputFile{
    margin: 0 auto;
    width: 650px;
    height: 100px;
    background-color: rgba($color: #e0ffff, $alpha: 1.0);
    padding: 5px 5px;
    &--errorType{
      background-color: rgba($color: #add6e5, $alpha: 1.0);
    }
    &--complite{
      background-color: rgba($color: #e0ffff, $alpha: 1.0);
    }
  &__block{
    width: 100%;
    height: 100%;
    border: 3px dashed #716c6c;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    cursor: pointer;
  }
  &__in{
    opacity: 0;
    width: 100%;
    height: 100%;
    margin-bottom: -75px;
  }
  &__text{
    margin: auto 0 ;
    color: #8a826f;
    text-align: center;
    font-size: 16px;
    line-height: 1.2;
    width: 200px;
    height: 50px;
  }
}
</style>
