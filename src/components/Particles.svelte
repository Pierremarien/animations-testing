<script lang="ts">
  import { onMount } from "svelte";
  let canvas: HTMLCanvasElement;
  let pen: CanvasRenderingContext2D;
  let startingTime = new Date().getTime();

  onMount(() => {
    pen = canvas.getContext("2d") as CanvasRenderingContext2D;
    drawings();
  });

  const drawings = () => {
   
    const currentTime = new Date().getTime();
    const elapsedTime = (currentTime - startingTime) / 1000;

    canvas.width = canvas.clientWidth;
    canvas.height = canvas.clientHeight;
   

    const circle = (x: number, y: number, radius: number) => {
        pen.beginPath();
        pen.arc(x, y, radius, 0, 2 * Math.PI);
        pen.fill();
        pen.stroke();
    }

    //this function draws stars, squares, triangles, octagons, ...
    const drawStar = (o: {cx: number, cy: number, outerRadius: number, innerRadius: number, numPoints: number, lineWidth: number, stroke: string, fill?: string, rotate?: number}) => {
    pen.lineWidth = o.lineWidth;
    pen.strokeStyle = o.stroke;

    const draw = (radius: number, angle: number, action: 'moveTo' | 'lineTo') => {
        let x = o.cx + radius * Math.cos(angle);
        let y = o.cy + radius * Math.sin(angle);
        pen[action](x, y);
    };

    draw(o.outerRadius, o.rotate || 0, 'moveTo');

    let angle = 2 * Math.PI / o.numPoints;

    for (let i = 0; i <= o.numPoints; i++) {
        let outerAngle = i * angle + (o.rotate || 0);
        let innerAngle = outerAngle + angle / 2;

        draw(o.outerRadius, outerAngle, 'lineTo');
        draw(o.innerRadius, innerAngle, 'lineTo');
    }

    pen.stroke();

    if (o.fill) {
        pen.fillStyle = o.fill;
        pen.fill();
    }
};


    circle(300, 400, 50);

    drawStar({
    cx: 400,
    cy: 100,
    outerRadius: 40,
    innerRadius: 20,
    numPoints: 1000,
    lineWidth: 4,
    stroke: '#622569',
    fill: '#b8a9c9',
    rotate: 0
});
    
    requestAnimationFrame(drawings);
  };
</script>

<canvas bind:this={canvas} id="wrapper" />

<style lang="scss">
  #wrapper {
    width: 100vw;
    height: 100vh;
  }
</style>
