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
    let y = boardHeight - 30;

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

    //Buttons events
    document.addEventListener("keydown", keyDownHandler, false);
    document.addEventListener("keyup", keyUpHandler, false);

    //Brick description
    const brickRowCount = 3;
    const brickColumnCount = 5;
    const brickWidth = 75;
    const brickHeight = 20;
    const brickPadding = 10;
    const brickOffsetTop = 30;
    const brickOffsetLeft = 30;

    var bricks = [];
    for (var c = 0; c < brickColumnCount; c++) {
      bricks[c] = [];
      for (var r = 0; r < brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0 };
      }
    }

    const drawBricks = () => {
      for (var c = 0; c < brickColumnCount; c++) {
        for (var r = 0; r < brickRowCount; r++) {
          var brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
          var brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
          bricks[c][r].x = brickX;
          bricks[c][r].y = brickY;
          ctx.beginPath();
          ctx.rect(brickX, brickY, brickWidth, brickHeight);
          ctx.fillStyle = "#0095DD";
          ctx.fill();
          ctx.closePath();
        }
      }
    };

    const drawBall = () => {
      ctx.beginPath();
      ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
      ctx.fillStyle = "#0095DD";
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

      ctx.beginPath();
      ctx.rect(paddleX, boardHeight - paddleHeight, paddleWidth, paddleHeight);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath;
    };

    const draw = () => {
      drawBricks();
      ctx.clearRect(0, 0, boardWidth, boardHeight);
      drawBall();
      drawPaddle();

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
          alert("GAME OVER");
          document.location.reload();
          clearInterval(interval);
        }
      }

      x += dx;
      y += dy;
    };

    const interval = setInterval(draw, 10);
  });
</script>

<style>
  canvas {
    border: 1px solid pink;
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
