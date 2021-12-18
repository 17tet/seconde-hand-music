<template>
  <video id="input_video" muted autoplay></video>
</template>

<script setup>
import { onMounted } from 'vue';

onMounted(()=>{
  let detections = {};
  const videoElement = document.getElementById('input_video');
  function getHands(results) {
    detections = results;
  }
  const hands = new Hands({locateFile: (file) => {
    return `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`;
  }});
  hands.setOptions({
    maxNumHands: 2,
    modelComplexity: 1,
    minDetectionConfidence: 0.5,
    minTrackingConfidence: 0.5
  });
  hands.onResults(getHands);
  const camera = new Camera(videoElement, {
    onFrame: async () => {
      await hands.send({image: videoElement});
    },
    width: 800,
    height: 640
  });
  camera.start();
    let canvas;
  let circles = [];
  let tt = 0;
  const TIMEOUT = 1;
  const music = [];

  const colorbank = [
    60,
    120,
    180,
    240,
    300
  ]

  let area; // add
  let sketch = function (p) {
    p.preload = () => {
      music[0] = p.loadSound("assets/Do.m4a");
      music[1] = p.loadSound("assets/Fa.mp3");
      music[2] = p.loadSound("assets/La.m4a");
      music[3] = p.loadSound("assets/Mi.m4a");
      music[4] = p.loadSound("assets/Re.m4a");
    }
    p.setup = function () {
      canvas = p.createCanvas(800, 640);
      canvas.id("canvas");

      p.colorMode(p.HSB);
    };

    p.draw = function () {
      // let draw_keyboard = true;
      p.clear();

  //     // Draw a keyboard
  //     if (draw_keyboard) {
  //       let x = 0;
  //       for (let i = 0; i < 10; i++) {
  //         // Draw the key
  //         let border_stroke_color;
  //         border_stroke_color = p.stroke(0);
  //         p.strokeWeight(1);
  //         p.noFill();
  //         p.rect(i * 80, 0, 80, p.height);
  //         x += 100;
  //       }

        p.drawCircle()
      //   draw_keyboard = false;
      // }

      if (detections != undefined) {
        if (detections.multiHandLandmarks != undefined) {
          //p.drawHands();
          // p.drawParts();

          // p.drawLines([0, 5, 9, 13, 17, 0]); //palm
          // p.drawLines([0, 1, 2, 3, 4]); //thumb
          // p.drawLines([5, 6, 7, 8]); //index finger
          // p.drawLines([9, 10, 11, 12]); //middle finger
          // p.drawLines([13, 14, 15, 16]); //ring finger
          // p.drawLines([17, 18, 19, 20]); //pinky

          // p.drawLandmarks([0, 1], 0); //palm base
          p.drawLandmarks([4,5], 60); //thumb
          p.drawLandmarks([8,9], 120); //index finger
          p.drawLandmarks([12, 13], 180); //middle finger
          p.drawLandmarks([16, 17], 240); //ring finger
          p.drawLandmarks([20, 21], 300); //pinky
        }
      }
    };
    p.drawCircle = () => {
      if(circles.length < 5){
        p.generateCircle(circles.length) // add
      }else{
        tt++;
        for(let i = 0; i < circles.length; i++){
          p.strokeWeight(4);
          p.stroke(255);
          p.fill(circles[i].a, 40, 255, 0.5);
          p.circle(circles[i].x, circles[i].y, circles[i].size);
        }
        if(tt > TIMEOUT)
        {
          for(let i = 0; i < circles.length; i++){
            circles[i].size--;
            if(circles[i].size < 5){
              circles[i].size = p.random(60, area) // add// add
              circles[i].y = p.random(50, p.height - 100) // add
              // circles.splice(i,1)
              // music[i].play();
            }
          }
          tt = 0;
        }
      }
    }
    p.circleCrash = (x, y) => {
      for(let i = 0; i < circles.length; i++){
          if(p.dist(x, y, circles[i].x, circles[i].y) < circles[i].size){
            // circles.splice(i,1)
            console.log(x);
            console.log(y);
            circles[i].size = 6
            music[i].play();
          }
      }
    }
    p.generateCircle = (index) => {

      area = p.int(p.width / 9); // add
      console.log(20 + index * (area + area) , 20 + index * (area + area)  + area)
      let x = p.random(20 + index * (area + area) , 20 + index * (area + area)  + area)
      let y = p.random(50, p.height - 100)
      let a = p.random(colorbank);
      let b = p.random(0, 255);
      let c = p.random(0, 255);

        circles.push({
          x: x,
          y: y,
          a: a,
          b: b,
          c: c,
          size: p.random(60, area)
        })

    }
    p.drawHands = function () {
      for (let i = 0; i < detections.multiHandLandmarks.length; i++) {
        for (let j = 0; j < detections.multiHandLandmarks[i].length; j++) {
          let x = detections.multiHandLandmarks[i][j].x * p.width;
          let y = detections.multiHandLandmarks[i][j].y * p.height;

          let z = detections.multiHandLandmarks[i][j].z;
          p.strokeWeight(0);
          p.textFont('Helvetica Neue');
          p.text(j, x, y);
          p.stroke(255);
          p.strokeWeight(10);
          p.point(x, y);
        }
      }
    };

    p.drawLandmarks = function (indexArray, hue) {
      p.noFill();
      p.strokeWeight(8);
      for (let i = 0; i < detections.multiHandLandmarks.length; i++) {
        for (let j = indexArray[0]; j < indexArray[1]; j++) {
          let x = detections.multiHandLandmarks[i][j].x * p.width;
          let y = detections.multiHandLandmarks[i][j].y * p.height;
          // console.log("For hand " + i + ", 8 position " + detections.multiHandLandmarks[i][8].x * 100)
          let z = detections.multiHandLandmarks[i][j].z;

          p.stroke(hue, 40, 255);
          p.point(x, y);
          p.circleCrash(x, y); //
          // if (
          //   detections.multiHandLandmarks[i][8].x > 0 &&
          //   detections.multiHandLandmarks[i][8].x < 0.1 &&
          //   detections.multiHandLandmarks[i][8].y > 0 &&
          //   detections.multiHandLandmarks[i][8].y < p.height
          // ) {
          //   p.rect(0, 0, 80, p.height);
          // } else if (
          //   detections.multiHandLandmarks[i][8].x > 0.1 &&
          //   detections.multiHandLandmarks[i][8].x < 0.2 &&
          //   detections.multiHandLandmarks[i][8].y > 0 &&
          //   detections.multiHandLandmarks[i][8].y < p.height
          // ) {
          //   p.rect(80, 0, 80, p.height);
          // }
        }
      }
    };

    p.drawLines = function (index) {
      p.stroke(0, 0, 255);
      p.strokeWeight(3);
      for (let i = 0; i < detections.multiHandLandmarks.length; i++) {
        for (let j = 0; j < index.length - 1; j++) {
          let x = detections.multiHandLandmarks[i][index[j]].x * p.width;
          let y = detections.multiHandLandmarks[i][index[j]].y * p.height;
          // let z = detections.multiHandLandmarks[i][index[j]].z;

          let _x = detections.multiHandLandmarks[i][index[j + 1]].x * p.width;
          let _y = detections.multiHandLandmarks[i][index[j + 1]].y * p.height;
          // let _z = detections.multiHandLandmarks[i][index[j+1]].z;
          p.line(x, y, _x, _y);
        }
      }
    };
  };

  let myp5 = new p5(sketch);
})


</script>

<style>
  #canvas {
    transform : scale(-1, 1);
    position: absolute;
    z-index: 99;
    top: 50px;
    left: 0;
    right:0;
    margin: 0 auto;
  }

  #input_video {
    transform : scale(-1, 1);
    position: absolute;
    z-index: -1;
    border: 3px #fff solid;
    border-radius: 10px;
    top: 50px;
    left: 0;
    right:0;
    margin: 0 auto;
  }
</style>