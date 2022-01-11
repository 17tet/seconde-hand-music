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

  jQuery(function ($) {
    // Here, $ === jQuery
    $(window).on("load", function () {
      $(".loader-wrapper").fadeOut("slow");
    });
  });

  //#region P5.JS
  let canvas;
  let circles = [];
  let tt = 0;
  let timer = 0;
  const TIMEOUT = 1;

  const music_bell = [];
  const music_piano = [];
  const music_elec = [];

  const instrument = [];
  const active_instrument = [];
  let active_music;

  let elec;
  let piano;
  let bell;
  const instruments_pos = [{"x": 267,"y": 100},{"x": 400,"y": 100},{"x": 533,"y": 100}];

  let elec_inst;
  let piano_inst;
  let bell_inst;

  let active_color = 0;
  const colorbank = [60, 120, 180, 240, 300];

  let active_button = true;
  let start_button;
  let free_button;

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

      
      start_button = p.loadImage("assets/start_button.png");
      free_button = p.loadImage("assets/free_button.png");
    };


    p.setup = function () {
      canvas = p.createCanvas(800, 640);
      canvas.id("canvas");

      p.colorMode(p.HSB);

      active_music = music_piano;

      elec_inst = instrument[0];
      piano_inst = instrument[1];
      bell_inst = instrument[2];

      piano_inst = active_instrument[1];
    };


    p.draw = function () {
      p.clear();

      if (active_button){
        p.image(start_button, p.width/2 - 150, p.height/2 - 100);
        p.image(free_button, p.width/2 - 150, p.height/2);
        // p.strokeWeight(1);
        // p.stroke(215.6, 42.9, 24.7);
        // p.fill(215.6, 42.9, 24.7);
        // p.rect(p.width/2 - 110, p.height/2 + 10, 200, 100);
        // p.fill(255);
        // p.rect(p.width/2 - 100, p.height/2, 200, 100);
        //p.text("TRATS", p.width/2 - 120, p.height/2 + 25);
      }
      else {
        
        p.drawCircle();
      }


      elec = p.image(elec_inst, (instruments_pos[0].x - 32), (instruments_pos[0].y - 32), 64, 64);
      piano = p.image(piano_inst, (instruments_pos[1].x - 32), (instruments_pos[1].y - 32), 64, 64);
      bell = p.image(bell_inst, (instruments_pos[2].x - 32), (instruments_pos[2].y - 32), 64, 64);

      //

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


    p.StartGame = (x, y, finger_index) => {
      if (finger_index == 8) {
        if (x > p.width/2 - 150 && x < p.width/2 + 150 && y > p.height/2 - 100 && y < p.height/2 - 9) {
          console.log("Start Game");
          active_button = false;
          p.drawCircle();
          return;
        }
      return;
      }
    };

    p.changeActiveInstrument = (x, y, finger_index) => {
      p.strokeWeight(0);
      p.fill(0, 100, 255, 0.5);
      let time_dif = 0;
      if (finger_index == 8) {
        time_dif = p.millis() - timer
        if (p.dist(x, y, instruments_pos[0].x, instruments_pos[0].y) < 64) {
          if (time_dif > 2000){
            p.changeInstrument(1);
            time_dif = 0
          }else{
            p.circle(instruments_pos[0].x, instruments_pos[0].y, 64 * (time_dif/2000));
          }
          return;
        }else if (p.dist(x, y, instruments_pos[1].x, instruments_pos[1].y) < 64) {
          //console.log("Middle");
          if (time_dif > 2000){
            p.changeInstrument(0);
            time_dif = 0
          }else{
            p.circle(instruments_pos[1].x, instruments_pos[1].y, 64 * (time_dif/2000));
          }
          return;
        }else if (p.dist(x, y, instruments_pos[2].x, instruments_pos[2].y) < 64) {
          //console.log("Left");
          if (time_dif > 2000){
            p.changeInstrument(-1);
            time_dif = 0
          }else{
            p.circle(instruments_pos[2].x, instruments_pos[2].y, 64 * (time_dif/2000));
          }
          return;
        }else{
          timer = p.millis();
        }
        return;
      }
    };

    p.changeInstrument = (changement) => {
      console.log("Change instrument");
      switch (changement) {
        case -1:
          elec_inst = instrument[0];
          piano_inst = instrument[1];
          bell_inst = active_instrument[2];
          active_music = music_bell;
          p.redraw();
          break;
        case 1:
          elec_inst = active_instrument[0];
          piano_inst = instrument[1];
          bell_inst = instrument[2];
          active_music = music_elec;
          p.redraw();
          break;
        case 0:
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
          p.circle(circles[i].x, circles[i].y + 100, circles[i].size);
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
          p.circleCrash(x, y - 100, j);
          p.changeActiveInstrument(x, y, j);
          if(active_button){
            p.StartGame(x,y,j);
          }
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

//#endregion
</script>

<style>
#canvas {
  display: flex;
  transform: scale(-1, 1);
  position: relative;
  z-index: 1;
  margin-top: -900px;
  /* margin-top: 45px; */
  justify-content: space-around;
  margin-right: auto;
  margin-left: auto;
  text-align: center;
}


#input_video {
  transform: scale(-1, 1);
  z-index: -1;
  border: 3px #fff solid;
  border-radius: 10px;
  top: 150px;
  /* margin-left: 110px; */
  /* right: 0;
  margin: 0 auto; */
}

/* @media (max-width: 1250px) {
  #input_video {
    display: flex;
    justify-content: center;
    margin-left: auto;
    margin-right: auto;
  }

  #canvas {
    display: flex;
    justify-content: center;
    margin-left: auto;
    margin-right: auto;
  }
}

@media (max-width: 1650px) {
  #input_video {
    display: flex;
    justify-content: center;
    margin-left: auto;
    margin-right: auto;
  }

  #canvas {
    display: block;
    justify-content: space-around;
    padding-left: 0;
    padding-right: 0;
    margin-left: auto;
    margin-right: auto;
  }
} */
</style>