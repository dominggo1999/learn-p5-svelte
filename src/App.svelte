<script>
import { tw, apply } from 'twind';
import P5 from './lib/P5.svelte';
import { AppContainer } from './App.style';

const width = 500;
const height = 500;
const xoff1 = 0;
const xoff2 = 10000;
const inc = 0.005;
let start = 0;


const sketch = (p5) => {
  p5.setup = () => {
    p5.createCanvas(width, height);
  };

  p5.draw = () => {
    p5.background(0);
    p5.stroke(255);
    p5.noFill();
    p5.beginShape();
    let off = start;
    for (let i = 0; i < width; i += 1) {
      p5.stroke(255);
      p5.vertex(i, p5.noise(off) * height);
      off += inc;
    }
    p5.endShape();


    start += inc;

    // p5.noLoop();
    // const x = p5.map(p5.noise(xoff1),0, 1, 0, width);
    // const y = p5.map(p5.noise(xoff2),0, 1, 0, height);
    // xoff1+=0.003;
    // xoff2+=0.002;

    // p5.ellipse(x, y ,24, 24);
  };
};

</script>

<div class={AppContainer}>

  <P5 {sketch} />
</div>

