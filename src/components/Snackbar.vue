<template>
  <div class="snackbar" v-if="snackbar.show" :class="`mode-${snackbar.mode}`">
    <h4>{{ snackbar.message }}</h4>
  </div>
</template>

<script>
export default {
  name: "Snackbar",
  props: {
    snackbar: {
      type: Object,
      default: () => {
      }
    }
  },
  watch:{
    'snackbar.show'(val){
      if (val){
        setTimeout(()=>{
          this.$emit('close-snackbar')
        },5000)
      }
    }
  },
}
</script>

<style scoped>
.snackbar {
  width: 100%;
  height: 50px;
  margin: 0;
  position: fixed;
  bottom: 0;
  text-align: center;
  display: flex;
  align-items: center;
  justify-content: center;
}
.mode-error{
  background: #dc3545;
}
.mode-success{
  background: #28a745;
}
h4 {
  color: white;
  margin: 0;
}
</style>