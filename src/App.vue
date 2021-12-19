<template>
<div>
   <h1>Turn on your computer's volume and have fun:)</h1>
   <h2>Match the colour of your finger to the colour of the circle</h2>
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

 h1{
      box-sizing:border-box;
      margin:0;
      text-align: center;
      color:#55f8b4;
  }
  h2{
      box-sizing:border-box;
      margin:15;
      color:#4dfaecea;
      text-align: center;
  }
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
