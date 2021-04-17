<template>
 <InputFile></InputFile>
</template>

<script>
import InputFile from './components/InputFile.vue';

export default {
  name: 'App',
  components: {
    InputFile
  },
  data() {
    return {
      fileXl: null,
    }
  }
}
</script>

<style lang="scss">
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

</style>
