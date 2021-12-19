<template>
<div>
   <h1>Turn on your computer's volume and have fun:)</h1>
 </div>
  <videoEl></videoEl>
  
</template>

<script>
import videoEl from "./components/p5Video.vue"
import header from "./components/header.vue"
export default {
  name: 'App',
  components: {
    videoEl,
    'my-header':header
  }
}

</script>

<style>

 *{
      box-sizing:border-box;
      margin:0;
  }
  h1{color:#55f8b4;}
  body {
    background-color: rgba(0, 0, 0, 0.938);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .canvas{
    transform: scale(-1,-1,-1);
  }


</style>
