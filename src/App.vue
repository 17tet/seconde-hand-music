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

  <div class="content">
    <videoEl></videoEl>
    <div class="rules">
      <h2>How to play ?</h2>
      <p>
        ♫ Match the colour of your finger to the colour of the circle to play a
        note
      </p>
      <p>
        ♫ Switch the instruments by touching one of the instruments up above
        with your index
      </p>
    </div>
  </div>

  <div class="footer">
    <p class="footer_text">Made by WU Jianqiao and TEDJINI Meriem.</p>
  </div>
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
html {
  height: 100%;
}

body {
  background-color: #242f3f;
  margin: 0;
  color: #fff;
  height: 50vw;
  min-height: 100%;
  padding: 0;
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

h1 {
  box-sizing: border-box;
  margin-top: 25px;
  text-align: center;
}

.content {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}

h2 {
  box-sizing: border-box;
  text-align: center;
  margin-top: 50px;
}

.rules p {
  font-size: 24px;
}

.footer {
  display: flex;
  background-color: #24827c;
  position: absolute;
  margin-top: 50px;
  bottom: 0;
  left: 0;
  right: 0;
  height: 5vh;
  justify-content: center;
  align-items: center;
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

/* @media (max-width: 1024px) {
  .content {
    flex-wrap: wrap;
  } */
/* } */
</style>
