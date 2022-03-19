<script>
  import { onMount } from "svelte";
  const spawnPie = () => new Pie(Math.random() * window.innerWidth);

  let highScore  = 0 ,score = 0; 

  function Pie(x) {
    this.x = x;
    this.y = 0;
    this.evil = Math.random() < 0.2 ? true :false;
    this.loop = () => {
      this.y += 5 + (score > 35 ? 35 : score);
    }
  }

  let plate, pause, totalCount = 0, pie = spawnPie(), color = 100;

  const id = setInterval(() => {
    if (pause) return;
    document.querySelectorAll("div").forEach(e => {
      e.style['background-color']
      = `hsl(206, 100%, ${color}%)`
    })
    try {
      pie.loop();
      pie = pie;
      if((window.innerHeight - 60) < pie.y) {
        if ((plate.x < (pie.x + 30)) &&
        (pie.x  < (plate.x + window.innerWidth * 0.2))) {
          score++;
          if (pie.evil) {
            color = 100;
            score -= 4;
            totalCount--;
          }
          if (score > highScore)
            highScore = score;
            color-=3;
          } else {
            score--;
            if (pie.evil) score += 2;
            color+=3;
          };
            score = score < 0 ? 0: score ;
        if(color < 30) color = 30;
        else if(color > 100) color = 100;
        pie = spawnPie();
        totalCount++;
      }
    } catch (e) {
      console.error(e);
      clearInterval(id);
    }
  }, 30)

  function movePlate({ clientX }) {
    if (pause) return;
    plate.x = clientX - (window.innerWidth * 0.1);
    plate.style.left = `${plate.x}px`;
  }
  const stop = () => pause = true;
  const start = () => pause = false;
  onMount(() => {
    document.addEventListener("keydown",({ code }) => {
      if(code == "Space") pause = pause? 0 : 1;
    })
  })
</script>

<main on:mouseout={stop} on:blur={stop}  on:mousemove={movePlate} 
  on:mouseover={start} >
  <h1>{highScore} </h1> <h4> {score} / {totalCount}</h4>
  <div id="pie" style={`left: ${pie.x}px; top: ${pie.y}px; 
    background-color:${ pie.evil ? '#eb4934' : '#bbb' };`}
  ></div>
  <div id="plate" bind:this={plate} ></div>
</main>

<style lang="scss">
  main {
    @include absolute;
    @include flex(row);
    @include fullscreen;
    @include cover("../background.svg");

    color: $light;
    align-items: flex-start;
    justify-content: center;
    user-select: none;
  }
  div {
    @include shadow;
    background-color: $light;
  }
  #pie {
    @include section(30px,30px);
    position: absolute;
  }
  #plate {
    @include section(30px,20vw);
    position:fixed;
    bottom: 5vh;
    z-index: 3;
  }
</style>
