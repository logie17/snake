<template>
  <div class="Snake">
    <h1>SNAKE!</h1>
    <h1
      v-if="hitWall"
    >
      Game Over!
    </h1>
    <h2>Score: {{ score }}</h2>
    <div class="Snake_BoardWrapper">
      <div
        ref="board"
        class="Snake_Board"
      >
        <div
          v-for="bodyPart in snake"
          :key="bodyPart.id"
          class="Snake_Board_Body_Part"
          :style="`top:${bodyPart.y}.px; left:${bodyPart.x}px; color: red`"
        />
        <div
          class="Snake_Board_Food"
          :style="`top:${foodY}.px; left:${foodX}px; background-color:#${foodColor};`"
        />
      </div>
    </div>
  </div>
</template>

<script>
const width=200;
const height=200;
const cellSize=10;

export default {
  name: 'Snake',
  data() {
    return {
      hitWall: false,
      foodX: 0,
      foodY: 0,
      foodColor: Math.floor(Math.random()*16777215).toString(16),
      score: 0,
      snake: [
        { x: 10, y: 40 },
        { x: 10, y: 50 },
        { x: 10, y: 60 },
        { x: 10, y: 70 },
      ],
      dx: 10,
      dy: 0,
    }
  },
  async mounted() {
    document.addEventListener("keydown", this.changeDirection);
    this.sprinkleFood();
    setInterval(() => {
      this.changeDirection = false;
      this.moveSnake();
    }, 100);
  },
  methods: {
    sprinkleFood() {
      let foundLocation;
      let randomX;
      let randomY;
      while (!foundLocation) {
        randomX = Math.floor(Math.random() * Math.floor(width/cellSize)) * cellSize;
        randomY = Math.floor(Math.random() * Math.floor(height/cellSize)) * cellSize;
        console.log("food", randomX, randomY);
        const isOnSnake = this.snake.find(c => c.x == randomX && c.y == randomY);
        if (!isOnSnake) foundLocation = true;
      }
      this.foodX = randomX;
      this.foodY = randomY;
      this.foodColor = Math.floor(Math.random()*16777215).toString(16);
    },
    changeDirection(event) {
      const LEFT_KEY = 37;
      const RIGHT_KEY = 39;
      const UP_KEY = 38;
      const DOWN_KEY = 40;
      const keyPressed = event.keyCode;
      const goingUp = this.dy === cellSize * -1;
      const goingDown = this.dy === cellSize;
      const goingRight = this.dx === cellSize;
      const goingLeft = this.dx === cellSize * -1;

      this.hitwall = false;
      if (keyPressed === LEFT_KEY && !goingRight) {
        this.dx = -10;
        this.dy = 0;
      }
      if (keyPressed === UP_KEY && !goingDown) {
        this.dx = 0;
        this.dy = -10;
      }
      if (keyPressed === RIGHT_KEY && !goingLeft) {
        this.dx = 10;
        this.dy = 0;
      }
      if (keyPressed === DOWN_KEY && !goingUp) {
        this.dx = 0;
        this.dy = 10;
      }
    },
    moveSnake() {
      const head = { x: this.snake[0].x + this.dx, y: this.snake[0].y + this.dy };
      if (head.x >= 200 || head.y >= 200 || head.y < 0 || head.x < 0) {
        this.dx = 0;
        this.dy = 0;
        this.hitWall = true;
//        return;
      }

      const hitSnake = this.snake.find(c => c.x == head.x && c.y == head.y);
      if (hitSnake) {
        this.dx = 0;
        this.dy = 0;
        this.hitWall = true;
//        return;
      }

      if (!this.hitWall) {
        this.snake.unshift(head);
        this.snake.pop();
        if (head.x == this.foodX && head.y == this.foodY) {
          this.score += 1;
          this.snake.unshift(head);
          this.sprinkleFood();
        }
      }
    }
  }
}
</script>

<style lang="scss">
  .Snake {
    display: flex;
    flex-flow: row wrap;
    font-weight: bold;
    text-align: center;
//    align-items: center;
//    justify-content: center;

    H1, H2 {
      flex: 1 100%;
      display: block;
    }

    &_BoardWrapper {
      flex: 1 100%;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    &_Board {
      border: 1px solid grey;
      height: 200px;
      width: 200px;
      position: relative;

      &_Food {
        width: 10px;
        height: 10px;
        position: absolute;
        background-color: yellow;
      }
      &_Body {

        &_Part {
          width: 10px;
          height: 10px;
          position: absolute;
          background-color: red;

        }
      }
    }

  }
</style>
