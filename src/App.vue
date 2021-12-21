<template>
  <div>
    <div class="header">
      <p class="title">Second Hand Music</p>
    </div>

    <h1>Turn up your computer's volume and have fun ! :)</h1>
  </div>

  <div class="loader-wrapper">
    <span class="loader"><span class="loader-inner"></span></span>
  </div>

  <videoEl></videoEl>

  <h2>
    Match the colour of your finger to the colour of the circle, you can also
    switch instruments by touching the instrument with your index
  </h2>

  <div class="footer"></div>
</template>

<script>
import videoEl from "./components/p5Video.vue";
import header from "./components/header.vue";
export default {
  name: "App",
  components: {
    videoEl,
    "my-header": header,
  },
};
</script>

<style>
body {
  background-color: #242f3f;
  margin: 0;
  color: #fff;
}

.header {
  display: flex;
  background-color: #24827c;
  margin-top: 0;
  height: 5vh;
  justify-content: center;
  align-items: center;
}

.title {
  font-size: 32px;
}

.footer {
  display: flex;
  background-color: #24827c;
  bottom: 0;
  height: 5vh;
  justify-content: center;
  align-items: center;
}

h1 {
  box-sizing: border-box;
  margin: 0;
  text-align: center;
}
h2 {
  box-sizing: border-box;
  text-align: center;
}

.canvas {
  transform: scale(-1, -1, -1);
}

.loader-wrapper {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background-color: #242f3f;
  display: flex;
  justify-content: center;
  align-items: center;
}
.loader {
  display: inline-block;
  width: 30px;
  height: 30px;
  position: relative;
  border: 4px solid #fff;
  animation: loader 2s infinite ease;
}
.loader-inner {
  vertical-align: top;
  display: inline-block;
  width: 100%;
  background-color: #fff;
  animation: loader-inner 2s infinite ease-in;
}
@keyframes loader {
  0% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(180deg);
  }
  50% {
    transform: rotate(180deg);
  }
  75% {
    transform: rotate(360deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@keyframes loader-inner {
  0% {
    height: 0%;
  }
  25% {
    height: 0%;
  }
  50% {
    height: 100%;
  }
  75% {
    height: 100%;
  }
  100% {
    height: 0%;
  }
}
</style>
