<template>
  <div class="row" @keydown.space="increase">
    <div class="score">
      Score: {{ score }} <br />
      Current Speed: {{ time.toFixed(1) }}
      <br />
    </div>
    <div class="high_score">
      <strong>HIGH SCORE: {{ high_score }}</strong>
    </div>
    <div ref="box" class="box" id="box"></div>
    <div class="line" id="line"></div>
    <div class="info-container">
      <div class="info-btn">info?</div>
      <div class="info-box">
        <p>Press Space Bar or Enter to Start Game</p>
        <p>
          Press the space bar when the square is touching the center line. The
          square moves faster with every point earned.
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import { TimelineLite, Power0 } from "gsap";
import Draggable from "gsap/Draggable";
import lf from "localforage";
export default {
  name: "Box",
  data: function() {
    return {
      time: 1.0,
      timeline: new TimelineLite(),
      score: 0,
      hits: 0,
      misses: 0,
      running: false,
      high_score: 0
    };
  },
  mounted() {
    window.addEventListener("keydown", e => {
      if (e.keyCode == 32) {
        console.log("space");
        this.checkHit();
      }
      if (e.keyCode == 13) {
        this.start();
      }
    });
    this.timeline.to("#box", 5, {
      y: "85vh",
      ease: Power0.easeInOut,
      yoyo: true,
      repeat: -1
    });
    this.timeline.pause();

    lf.getItem("high_score")
      .then(value => {
        console.log("High score: " + value);
        if (value == null) {
          console.log("no high score found");
          lf.setItem("high_score", 0).then(value => {
            this.high_score = 0;
            console.log("high score set to " + value);
          });
        } else {
          this.high_score = value;
        }
      })
      .catch(function(err) {
        console.log("Error: " + err);
        // we got an error
      });
  },
  methods: {
    increase() {
      this.checkHit();
    },

    start() {
      if (this.running == false) {
        this.time = 1;
        this.timeline.timeScale(this.time);
        this.timeline.play();
        this.running = true;
      } else {
        this.time = 1;
        this.score = 0;
        this.hits = 0;
        this.misses = 0;
        this.timeline.timeScale(this.time);
        this.timeline.time(0).pause();
        this.running = false;
      }
    },

    checkHit() {
      if (Draggable.hitTest("#box", "#line")) {
        console.log("hit");
        this.time += 0.1;
        this.timeline.timeScale(this.time);
        this.score += 1;
        this.hits += 1;
      } else {
        this.time = 1;
        this.endGame();
      }
    },

    endGame() {
      if (this.score > this.high_score) {
        alert("HIGH SCORE " + this.score);
        lf.setItem("high_score", this.score).then(value => {
          this.high_score = value;
        });
        this.start();
      } else if (this.score == 0 && this.running == true) {
        this.start();
        alert("Your score was 0...Really!? \nYou can do better than that.");
      } else if (this.running == true) {
        alert(
          `GAME OVER. TRY AGAIN \nSCORE: ${this.score} \nYou need ${this
            .high_score - this.score} more points for high score.`
        );
        this.start();
      } else {
        this.start();
      }
    },

    changeColor() {
      return (
        "#" +
        (function co(lor) {
          return (lor += [
            0,
            1,
            2,
            3,
            4,
            5,
            6,
            7,
            8,
            9,
            "a",
            "b",
            "c",
            "d",
            "e",
            "f"
          ][Math.floor(Math.random() * 16)]) && lor.length == 6
            ? lor
            : co(lor);
        })("")
      );
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.row {
  display: flex;
  justify-content: center;
  height: 98vh;
  width: 100vw;
  margin: 0px;
  padding: 0px;
}
.box {
  height: 10vh;
  width: 10vh;
  background: rebeccapurple;
  margin-top: 2vh;
}
.line {
  height: 10px;
  width: 100vw;
  background: turquoise;
  position: fixed;
  top: 45%;
  margin: 0;
}
.score {
  position: absolute;
  top: 10px;
  left: 10px;
}

.info-btn {
  padding: 5px 10px;
  border: 1px solid black;
  border-radius: 10px;
  margin-top: 20px;
  cursor: pointer;
  width: 100px;
  margin: 0 auto;
}

.info-container {
  margin-top: 10px;
  position: fixed;
  bottom: 20px;
  width: 100vw;
}

.info-container:hover .info-box {
  opacity: 1;
  display: block;
}

.info-box {
  opacity: 0;
  transition: opacity 0.5s;
  position: fixed;
  bottom: 15px;
  margin: 0 auto;
  width: 100vw;
  background: green;
  padding: 20px;
  color: white;
}

.high_score {
  position: absolute;
  right: 20px;
  top: 20px;
}
</style>
