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

    const drawBall = () => {
      ctx.beginPath();
      ctx.arc(x, y, ballRadius, 0, Math.PI * 2);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath();
    };

    //Paddle Parametres
    const paddleHeight = 10;
    const paddleWidth = 75;
    const paddleX = (boardWidth - paddleWidth) / 2;

    const drawPaddle = () => {
      ctx.beginPath();
      ctx.rect(paddleX, boardHeight - paddleHeight, paddleWidth, paddleHeight);
      ctx.fillStyle = "#0095DD";
      ctx.fill();
      ctx.closePath;
    };

    const draw = () => {
      ctx.clearRect(0, 0, boardWidth, boardHeight);
			drawBall();
			drawPaddle();

      if (x + dx > canvas.width - ballRadius || x + dx < ballRadius) {
        dx = -dx;
      }
      if (y + dy > canvas.height - ballRadius || y + dy < ballRadius) {
        dy = -dy;
      }

      x += dx;
      y += dy;
    };

    setInterval(draw, 10);
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
