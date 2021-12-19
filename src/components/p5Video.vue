<template>
  <video id="input_video" muted autoplay></video>
</template>

<script setup>
import { onMounted } from "vue";

onMounted(() => {
  let detections = {};
  const videoElement = document.getElementById("input_video");
  function getHands(results) {
    detections = results;
  }
  const hands = new Hands({
    locateFile: (file) => {
      return `https://cdn.jsdelivr.net/npm/@mediapipe/hands/${file}`;
    },
  });
  hands.setOptions({
    maxNumHands: 2,
    modelComplexity: 1,
    minDetectionConfidence: 0.5,
    minTrackingConfidence: 0.5,
  });
  hands.onResults(getHands);
  const camera = new Camera(videoElement, {
    onFrame: async () => {
      await hands.send({ image: videoElement });
    },
    width: 800,
    height: 640,
  });
  camera.start();
  let canvas;
  let circles = [];
  let tt = 0;
  const TIMEOUT = 1;
  const music = [];
  const music_bell = [];
  const music_piano = [];
  const music_elec = [];

  const instrument = [];
  const active_instrument = [];
  let active_music

  let elec;
  let piano;
  let bell;

  let elec_inst;
  let piano_inst;
  let bell_inst;

  let active_inst;

  let active_color = 0;
  const colorbank = [60, 120, 180, 240, 300];

  let area; // add
  let sketch = function (p) {
    p.preload = () => {
      music_bell[0] = p.loadSound("assets/DO_BELL.mp3");
      music_bell[1] = p.loadSound("assets/SOL_BELL.mp3");
      music_bell[2] = p.loadSound("assets/LA_BELL.mp3");
      music_bell[3] = p.loadSound("assets/MI_BELL.mp3");
      music_bell[4] = p.loadSound("assets/RE_BELL.mp3");

      music_piano[0] = p.loadSound("assets/DO_PIANO.mp3");
      music_piano[1] = p.loadSound("assets/SOL_PIANO.mp3");
      music_piano[2] = p.loadSound("assets/LA_PIANO.mp3");
      music_piano[3] = p.loadSound("assets/MI_PIANO.mp3");
      music_piano[4] = p.loadSound("assets/RE_PIANO.mp3");

      music_elec[0] = p.loadSound("assets/DO_ELEC.mp3");
      music_elec[1] = p.loadSound("assets/SOL_ELEC.mp3");
      music_elec[2] = p.loadSound("assets/LA_ELEC.mp3");
      music_elec[3] = p.loadSound("assets/MI_ELEC.mp3");
      music_elec[4] = p.loadSound("assets/RE_ELEC.mp3");

      instrument[0] = p.loadImage("assets/elec.png");
      instrument[1] = p.loadImage("assets/piano.png");
      instrument[2] = p.loadImage("assets/bell.png");

      active_instrument[0] = p.loadImage("assets/active_elec.png");
      active_instrument[1] = p.loadImage("assets/active_piano.png");
      active_instrument[2] = p.loadImage("assets/active_bell.png");
    };

    p.setup = function () {
      canvas = p.createCanvas(800, 640);
      canvas.id("canvas");

      p.colorMode(p.HSB);

      active_music = music_piano

      elec_inst = instrument[0];
      piano_inst = instrument[1];
      bell_inst = instrument[2];

      piano_inst = active_instrument[1];
    };

    p.draw = function () {
      p.clear();

      elec = p.image(elec_inst, p.width / 2 - 182, 50, 64, 64);
      piano = p.image(piano_inst, p.width / 2 - 32, 50, 64, 64);
      bell = p.image(bell_inst, p.width / 2 + 118, 50, 64, 64);

      

      p.drawCircle();

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
          p.drawLandmarks([4, 5], 60); //thumb
          p.drawLandmarks([8, 9], 120); //index finger
          p.drawLandmarks([12, 13], 180); //middle finger
          p.drawLandmarks([16, 17], 240); //ring finger
          p.drawLandmarks([20, 21], 300); //pinky
        }
      }
    };

    p.changeActiveInstrument = (x, y, finger_index) => {
      if (finger_index == 8) {
        if (p.dist(x, y, p.width / 2 - 182, 50) < 64) {
          console.log("Right");
          p.changeInstrument(1);
          return
        } 
        if (p.dist(x, y, p.width / 2 - 32, 50) < 64) {
          console.log("Middle");
          p.changeInstrument(0);
          return
        }
        if (p.dist(x, y, p.width / 2 + 118, 50) < 64) {
          console.log("Left");
          p.changeInstrument(-1);
          return
        }
        return
      }
    };

    p.changeInstrument = (changement) => {
      console.log("Change instrument");
      switch (changement) {
        case -1:
          active_inst = active_instrument[-1];
          elec_inst = instrument[0];
          piano_inst = instrument[1];
          bell_inst = active_instrument[2];
          active_music = music_bell;
          p.redraw();
          break;
        case 1:
          active_inst = active_instrument[0];
          elec_inst = active_instrument[0];
          piano_inst = instrument[1];
          bell_inst = instrument[2];
          active_music = music_elec;
          p.redraw();
          break;
        case 0:
          active_inst = active_instrument[1];
          elec_inst = instrument[0];
          piano_inst = active_instrument[1];
          bell_inst = instrument[2];
          active_music = music_piano;
          p.redraw();
      }
    };

    p.drawCircle = () => {
      if (circles.length < 5) {
        p.generateCircle(circles.length); // add
      } else {
        tt++;
        for (let i = 0; i < circles.length; i++) {
          p.strokeWeight(4);
          p.stroke(255);
          p.fill(circles[i].a, 50, 255, 0.5);
          p.circle(circles[i].x, circles[i].y+100, circles[i].size);
        }
        if (tt > TIMEOUT) {
          for (let i = 0; i < circles.length; i++) {
            circles[i].size--;
            if (circles[i].size < 5) {
              circles[i].size = p.random(50, area); // add// add
              circles[i].y = p.random(50, p.height); // add
              // circles.splice(i,1)
              // music[i].play();
            }
          }
          tt = 0;
        }
      }
    };

    p.matchingColorwithFinger = (finger_index, circle_color) => {
      let match = false;
      switch (finger_index) {
        case 4:
          if (circle_color == 60) {
            match = true;
          }
          break;
        case 8:
          if (circle_color == 120) {
            match = true;
          }
          break;
        case 12:
          if (circle_color == 180) {
            match = true;
          }
          break;
        case 16:
          if (circle_color == 240) {
            match = true;
          }
          break;
        case 20:
          if (circle_color == 300) {
            match = true;
          }
          break;
        default:
          match = false;
          break;
      }
      return match;
    };

    p.circleCrash = (x, y, finger_index) => {
      for (let i = 0; i < circles.length; i++) {
        if (p.dist(x, y, circles[i].x, circles[i].y) < circles[i].size) {
          // circles.splice(i,1)
          if (p.matchingColorwithFinger(finger_index, circles[i].a)) {
            circles[i].size = 6;
            active_music[i].play();
          }
        }
      }
    };

    p.generateCircle = (index) => {
      area = p.int(p.width / 9); // add
      console.log(
        20 + index * (area + area),
        20 + index * (area + area) + area
      );
      let x = p.random(
        -8 + index * (area + area),
        -8 + index * (area + area) + area
      );
      let y = p.random(50, p.height - 100);
      let a = colorbank[active_color];
      let b = p.random(0, 255);
      let c = p.random(0, 255);
      active_color += 1;

      circles.push({
        x: x,
        y: y,
        a: a,
        b: b,
        c: c,
        size: p.random(60, area),
      });
    };
    p.drawHands = function () {
      for (let i = 0; i < detections.multiHandLandmarks.length; i++) {
        for (let j = 0; j < detections.multiHandLandmarks[i].length; j++) {
          let x = detections.multiHandLandmarks[i][j].x * p.width;
          let y = detections.multiHandLandmarks[i][j].y * p.height;

          let z = detections.multiHandLandmarks[i][j].z;
          p.strokeWeight(0);
          p.textFont("Helvetica Neue");
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
          p.circleCrash(x, y-100, j); 
          p.changeActiveInstrument(x,y,j);
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
});
</script>

<style>
#canvas {
  transform: scale(-1, 1);
  position: absolute;
  z-index: 99;
  top: 50px;
  left: 0;
  right: 0;
  margin: 0 auto;
}

#input_video {
  transform: scale(-1, 1);
  position: absolute;
  z-index: -1;
  border: 3px #fff solid;
  border-radius: 10px;
  top: 50px;
  left: 0;
  right: 0;
  margin: 0 auto;
}
</style>