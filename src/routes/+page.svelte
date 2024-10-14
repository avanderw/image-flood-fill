<script lang="ts">
  import { onMount } from "svelte";
  import Header from "./Header.svelte";
  let speed = 5;
  let canvas: HTMLCanvasElement;

  onMount(() => {
    let imageUrls = [
      "image-01.jpeg",
      "image-02.png",
      "image-03.png",
      "image-04.png",
      "image-05.png",
      "image-06.png",
      "image-07.jpeg",
      "image-08.png",
    ];
    let idx = 0;
    let image = new Image();
    image.src = imageUrls[idx];

    let context: CanvasRenderingContext2D;
    let row = 0;
    let animId: number;

    context = canvas.getContext("2d")!;
    image.onload = () => {
      const targetWidth = 350;
      const targetHeight = 700;
      if (image.width > image.height) {
        canvas.width = targetWidth;
        canvas.height = (image.height / image.width) * targetWidth;
      } else {
        canvas.height = targetHeight;
        canvas.width = (image.width / image.height) * targetHeight;
        if (canvas.width > targetWidth) {
          canvas.width = targetWidth;
          canvas.height = (image.height / image.width) * targetWidth;
        }
      }

      row = image.height;
      animId = requestAnimationFrame(animate);
    };

    function animate() {
      const hRatio = canvas.width / image.width;
      const vRatio = canvas.height / image.height;
      const ratio = Math.min(hRatio, vRatio);
      context.drawImage(
        image,
        0,
        row,
        image.width,
        1,
        0,
        0,
        image.width * ratio,
        row * ratio + 4
      );
      if (row > 0) {
        row = row - speed;
        requestAnimationFrame(animate);
      } else {
        image.src = imageUrls[++idx % imageUrls.length];
        cancelAnimationFrame(animId);
      }
    }
  });
</script>

<Header />

<form>
  <span>Slower</span>
  <input type="range" bind:value={speed} min="2" max="8" />
  <span>Faster</span>
</form>

<canvas id="canvas" bind:this={canvas} />

<h1>2D Image Flood Fill Effect</h1>
<p>
  Let's dive into the details of how the flood fill effect is achieved in this
  code:
</p>
<ol>
  <li>
    <p>
      The core of the flood fill effect is in the <code>animate</code> function.
      This function is called repeatedly using
      <code>requestAnimationFrame</code>, creating a smooth animation.
    </p>
  </li>
  <li>
    <p>The key part of the effect is how the image is drawn onto the canvas:</p>
    <pre><code class="lang-javascript"
        ><span class="hljs-built_in">context</span>.drawImage(
  <span class="hljs-built_in">image</span>,
  <span class="hljs-number">0</span>,
  <span class="hljs-built_in">row</span>,
  <span class="hljs-built_in">image</span>.<span class="hljs-built_in"
          >width</span
        >,
  <span class="hljs-number">1</span>,
  <span class="hljs-number">0</span>,
  <span class="hljs-number">0</span>,
  <span class="hljs-built_in">image</span>.<span class="hljs-built_in"
          >width</span
        > * ratio,
  <span class="hljs-built_in">row</span> * ratio + <span class="hljs-number"
          >4</span
        >
);
</code></pre>
    <p>
      Let&#39;s break down the parameters of this <code>drawImage</code> call:
    </p>
    <ul>
      <li><code>image</code>: The source image being drawn.</li>
      <li>
        <code>0, row</code>: The x and y coordinates in the source image where
        to start copying.
      </li>
      <li>
        <code>image.width, 1</code>: The width and height of the slice being
        copied from the source image. Note that the height is always 1 pixel.
      </li>
      <li>
        <code>0, 0</code>: The x and y coordinates on the canvas where to start
        drawing.
      </li>
      <li>
        <code>image.width * ratio</code>: The width to draw the image slice on
        the canvas, scaled to fit.
      </li>
      <li>
        <code>row * ratio + 4</code>: The height to draw on the canvas. This is
        key to the fill effect.
      </li>
    </ul>
  </li>
  <li>
    <p>
      The flood fill effect is created by progressively revealing more of the
      image from bottom to top:
    </p>
    <ul>
      <li>
        The <code>row</code> variable starts at <code>image.height</code> (the bottom
        of the image).
      </li>
      <li>
        In each frame, <code>row</code> is decreased by the <code>speed</code>
        value: <code>row = row - speed;</code>
      </li>
      <li>
        This means each frame draws a slightly larger portion of the image,
        creating the illusion of the image being &quot;filled&quot; from bottom
        to top.
      </li>
    </ul>
  </li>
  <li>
    <p>
      The <code>+ 4</code> in <code>row * ratio + 4</code> creates a slight overlap
      between frames. This ensures there are no gaps in the fill effect, even at
      higher speeds.
    </p>
  </li>
  <li>
    <p>
      The speed of the fill can be adjusted by the user via the slider, which
      changes the <code>speed</code> variable. A higher speed value means larger
      chunks of the image are revealed in each frame, making the fill effect faster.
    </p>
  </li>
  <li>
    <p>
      The effect continues until <code>row</code> becomes 0 or negative, at which
      point the entire image has been revealed.
    </p>
  </li>
  <li>
    <p>
      Once an image is fully revealed, the code moves to the next image in the
      array and starts the process again.
    </p>
  </li>
</ol>
<p>
  This approach creates a smooth, continuous fill effect that works for any
  image size, maintaining aspect ratio and fitting within the canvas bounds. The
  effect is visually interesting because it gradually reveals the image content,
  creating a sense of anticipation for the viewer.
</p>

<style>
  form {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-bottom: 1rem;
    gap: 1rem;
  }
  canvas {
    border: 1px solid black;
    border-radius: 1rem;
    display: block;
    margin: 0 auto;
  }
  code {
    color: var(--primary-4);
  }
  .lang-javascript {
    display: block;
    background-color: var(--neutral-4);
    padding: 1rem;
    border-radius: 0.5rem;
  }
  .hljs-built_in {
    color: var(--neutral-2);
  }
  .hljs-number {
    color: var(--secondary-4);
  }
</style>
