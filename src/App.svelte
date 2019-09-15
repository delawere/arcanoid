<script>
  import { onMount } from "svelte";

  let canvas;
  let boardWidth = 480;
  let boardHeight = 320;
  let colorStroke = "#999";

  onMount(() => {
    const ctx = canvas.getContext("2d");

    ctx.beginPath();
    ctx.rect(20, 40, 50, 50);
    ctx.fillStyle = "#FF0000";
    ctx.fill();
    ctx.closePath();

    ctx.beginPath();
    ctx.arc(240, 160, 20, 0, Math.PI * 2, false);
    ctx.fillStyle = "green";
    ctx.fill();
    ctx.closePath();

    ctx.beginPath();
    ctx.rect(160, 10, 100, 40);
    ctx.strokeStyle = "rgba(0, 0, 255, 0.5)";
    ctx.stroke();
    ctx.closePath();

    //Ball parametres
    const ballRadius = 10;

    //Ball position
    let x = boardWidth / 2;
    let y = boardHeight - 19.5;

    //Ball color
    let ballColor = "#0095DD";

    //Ball moves values
    let dx = 2;
    let dy = -2;

    //Paddle Parametres
    const paddleHeight = 10;
    const paddleWidth = 75;
    let paddleX = (boardWidth - paddleWidth) / 2;

    //Buttons state
    let leftPressed = false;
    let rightPressed = false;

    //Butons handlers
    const keyDownHandler = e => {
      if (e.key === "Right" || e.key === "ArrowRight") {
        rightPressed = true;
      }

      if (e.key === "Left" || e.key === "ArrowLeft") {
        leftPressed = true;
      }
    };

    const keyUpHandler = e => {
      if (e.key === "Right" || e.key === "ArrowRight") {
        rightPressed = false;
      }

      if (e.key === "Left" || e.key === "ArrowLeft") {
        leftPressed = false;
      }
    };

    const mouseDownHandler = () => {
      start = true;
    };

    const mouseMoveHandler = e => {
      const relativeX = e.clientX - canvas.offsetLeft;
      if (relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth / 2;
      }
    };

    //Buttons events
    document.addEventListener("keydown", keyDownHandler, false);
    document.addEventListener("keyup", keyUpHandler, false);

    //Mouse events
    canvas.addEventListener("mousedown", mouseDownHandler, false);
    document.addEventListener("mousemove", mouseMoveHandler, false);

    //Brick description
    const brickRowCount = 3;
    const brickColumnCount = 5;
    const brickWidth = 75;
    const brickHeight = 20;
    const brickPadding = 10;
    const brickOffsetTop = 30;
    const brickOffsetLeft = 30;

    let bricks = [];
    // state: 0 - disappear
    // state: 1 - appear
    for (let c = 0; c < brickColumnCount; c++) {
      bricks[c] = [];
      for (let r = 0; r < brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, state: 1 };
      }
    }

    //Score
    let score = 0;

    //Lives
    let lives = 3;

    //Game has begun
    let start = false;

    const drawStartText = () => {
      ctx.font = "bold 22px Arial";
      ctx.fillStyle = "#fff";
      ctx.fillText("PRESS LEFT MOUSE BUTTON TO START", 22, 140);
    };

    const drawBricks = () => {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          if (bricks[c][r].state === 1) {
            let brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
            let brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
            bricks[c][r].x = brickX;
            bricks[c][r].y = brickY;
            ctx.beginPath();
            ctx.rect(brickX, brickY, brickWidth, brickHeight);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();
          }
        }
      }
    };

    const collisionDetection = () => {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          let b = bricks[c][r];
          if (b.state === 1) {
            if (
              x > b.x &&
              x < b.x + brickWidth &&
              y > b.y &&
              y < b.y + brickHeight
            ) {
              dy = -dy;
              b.state = 0;
              ballColor = getRandomColor();
              score++;

              if (score === brickColumnCount * brickRowCount) {
                console.log("You win!");
                document.location.reload();
                clearInterval(interval);
              }
            }
          }
        }
      }
    };

    const drawScore = () => {
      ctx.font = "16px Arial";
      ctx.fillStyle = "#0095DD";
      ctx.fillText("Score: " + score, 8, 20);
    };

    const getRandomInt = (min, max) =>
      Math.floor(Math.random() * (Math.floor(max) - Math.ceil(min) + 1)) +
      Math.ceil(min);

    const getRandomColor = () => {
      const rgb = "rgb";
      let result = `${rgb}(${getRandomInt(0, 255)},${getRandomInt(
        0,
        255
      )},${getRandomInt(0, 255)})`;
      return result;
    };

    const drawBall = () => {
      ctx.beginPath();
      ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
      ctx.fillStyle = ballColor;
      ctx.fill();
      ctx.closePath();
    };

    const drawPaddle = () => {
      if (rightPressed) {
        paddleX += 7;
        if (paddleX + paddleWidth > canvas.width) {
          paddleX = canvas.width - paddleWidth;
        }
      }

      if (leftPressed) {
        paddleX -= 7;
        if (paddleX < 0) {
          paddleX = 0;
        }
      }

      if (!start) {
        x = paddleX + paddleWidth / 2;
        y = boardHeight - 19.5;
      }

      ctx.beginPath();
      ctx.rect(paddleX, boardHeight - paddleHeight, paddleWidth, paddleHeight);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath;
    };

    const drawLives = () => {
      ctx.beginPath();
      ctx.fillStyle = "red";
      ctx.moveTo(18.75, 10);
      ctx.bezierCurveTo(18.75, 10, 17.5, 6.25, 12.5, 6.25);
      ctx.bezierCurveTo(5, 6.25, 5, 15.625, 5, 12.5);
      ctx.bezierCurveTo(5, 20, 10, 23.75, 18.75, 30);
      ctx.bezierCurveTo(27.5, 23.75, 32.5, 20, 32.5, 12.5);
      ctx.bezierCurveTo(32.5, 15.625, 32.5, 6.25, 25, 6.25);
      ctx.bezierCurveTo(21.25, 6.25, 18.75, 9.25, 18.75, 10);
      ctx.fill();
      ctx.font = "16px Arial";
      ctx.fillStyle = "#0095DD";
      ctx.fillText(lives, canvas.width - 20, 20);
    };

    const draw = () => {
      ctx.clearRect(0, 0, boardWidth, boardHeight);
      drawBricks();
      drawBall();
      drawPaddle();
      collisionDetection();
      drawScore();
      drawLives();

      if (!start && lives === 3) {
        drawStartText();
      }

      if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) {
        dx = -dx;
      }
      if (y + dy < ballRadius) {
        dy = -dy;
      }

      if (y + dy > boardHeight - ballRadius) {
        if (x > paddleX - 10 && x < paddleX + paddleWidth + 10) {
          dy = -dy;
        } else {
          if (lives > 1) {
            lives--;
            start = false;
          } else {
            document.location.reload();
            clearInterval(interval);
          }
        }
      }

      if (start) {
        x += dx;
        y += dy;
      }
    };

    const interval = setInterval(draw, 10);
  });
</script>

<style>
  canvas {
    border: 1px solid pink;
    background: #000;
  }
</style>

<header>
  <h1>Arcanoid2019</h1>
</header>
<main>
  <div>
    <canvas bind:this={canvas} width={boardWidth} height={boardHeight} />
  </div>
</main>
