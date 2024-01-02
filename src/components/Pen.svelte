<script lang="ts">
  import { onMount } from "svelte";

  let canvas: HTMLCanvasElement;
  let pen: CanvasRenderingContext2D;
  let startingTime = new Date().getTime();
  
  const arcColors = [
    "#efe2e9",
    "#fbcadd",
    "#ffbacc",
    "#fec6c5",
    "#feccc1",
    "#fcdbca",
    "#e9e0b9",
    "#e9e8c9",
    "#ddedc8",
    "#d7f2d3",
    "#baf4e7",
    "#ccf5f9",
    "#c3f3ff",
    "#c7e9ff",
    "#c2daff",
    "#dbe0fe",
    "#e6dbfb",
    "#eadcfd",
    "#e8d9fa",
    "#e7daf6",
    "#ebe2e7",

  ];

  onMount(() => {
    pen = canvas.getContext("2d") as CanvasRenderingContext2D;
    draw();
  });

  const draw = () => {
    //setting the frame rate
    const currentTime = new Date().getTime();
    const elapsedTime = (currentTime - startingTime) / 1000;

    //setting the canvas size and a pen
    canvas.width = canvas.clientWidth;
    canvas.height = canvas.clientHeight;
    pen.strokeStyle = "white";
    pen.lineWidth = 5;

    //drawing the impact line and calculating the length
    const impactArea = () => {
      const start = () => {
        return { x: canvas.width * 0.1, y: canvas.height * 0.9 };
      };

      const end = () => {
        return { x: canvas.width * 0.9, y: canvas.height * 0.9 };
      };

      pen.lineCap = "round";

      pen.beginPath();
      pen.moveTo(start().x, start().y);
      pen.lineTo(end().x, end().y);
      pen.stroke();
      let length = end().x - start().x;
      return length;
    };
    
    //getting back the length and calculating the arc radius and the spacing between the arcs
    let length = impactArea();
    const initialArcRadius = length * 0.05;
    const spacing = (length / 2 - initialArcRadius) / arcColors.length;

    //drawing the arcs and the impact point number of arcs based on number of colors chosen
    const drawCircle = (i: number, arcRadius: number) => {
      const center = () => {
        return { x: canvas.width / 2, y: canvas.height * 0.9 };
      };
      pen.strokeStyle = arcColors[i];
      pen.beginPath();
      pen.arc(center().x, center().y, arcRadius, Math.PI, 2 * Math.PI);
      pen.stroke();
    };

    

    const impactPoint = (i: number, arcRadius: number) => {
      const center = () => {
        return { x: canvas.width / 2, y: canvas.height * 0.9 };
      };

      pen.strokeStyle = "white";
      
      //setting the angular velocity and angular displacement and making it loop for 15 minutes
      const maxAngle = 2 * Math.PI;
      const numberOfLoops = 50 - i;
      const angularVelocity = (maxAngle * numberOfLoops) / 900;
      const angularDisplacement = Math.PI + (elapsedTime * angularVelocity);
      const modulatedAngularDisplacement = angularDisplacement % maxAngle;
      const correctedAngularDisplacement = modulatedAngularDisplacement >= Math.PI ? modulatedAngularDisplacement : maxAngle - modulatedAngularDisplacement;

      const x = center().x + arcRadius * Math.cos(correctedAngularDisplacement);
      const y = center().y + arcRadius * Math.sin(correctedAngularDisplacement);

      pen.fillStyle = "white";
      pen.beginPath();
      pen.arc(x, y, length * 0.005, 0, 2 * Math.PI);
      pen.fill();
      pen.stroke();
    };

    for (let i = 0, arrayLength = arcColors.length; i < arrayLength; i++) {
      const arcRadius = initialArcRadius + (i * spacing);
      drawCircle(i, arcRadius);
      impactPoint(i, arcRadius);
    }

    requestAnimationFrame(draw);
  };
</script>

<canvas bind:this={canvas} id="wrapper" />

<style lang="scss">
  #wrapper {
    width: 100vw;
    height: 100vh;
  }
</style>
