<script lang="ts">
  import cvlib from "@techstark/opencv-js";

  let cv: typeof cvlib;
  let ready = false;

  window.setTimeout(() => {
    cv = cvlib;
    ready = true;
  }, 1000);

  const onFileChange = (e) => {
    const img = new Image();
    img.src = URL.createObjectURL(e.target.files[0]);
    img.onload = () => {
      let src = cv.imread(img);

      // Check if the image is larger than 600x600
      if (src.cols > 800 || src.rows > 800) {
        // Resize the image
        const hratio = 800 / src.rows;
        const vratio = 800 / src.cols;
        const ratio = Math.min(hratio, vratio);
        cv.resize(
          src,
          src,
          new cv.Size(Math.round(src.cols * ratio), Math.round(src.rows * ratio)),
        )
      }
      
      console.log(
        `image width: ${src.cols}\n` +
          `image height: ${src.rows}\n` +
          `image size: ${src.size().width}*${src.size().height}\n` +
          `image depth: ${src.depth()}\n` +
          `image channels ${src.channels()}\n` +
          `image type: ${src.type()}\n`,
      )

      cv.imshow("outputCanvas", src);
      src.delete();
    };
  };
</script>

<main class="app">
  {#if ready}
    <h1>OpenCV 4.7</h1>
    <div class="canvasContainer">
      <canvas id="outputCanvas" />
    </div>
    <div id="fileInputField" class="field">
      <p class="description">Select an image file to load into OpenCV.js</p>
      <input type="file" id="fileInput" name="file" on:change={onFileChange} />
    </div>
  {:else}
    <h1>OpenCV is loading</h1>
  {/if}
</main>

<style>
  .app {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  .canvasContainer {
    flex: 1;
  }
  .field {
    margin: 1rem 0;
    display: flex;
    flex-direction: column;
  }

  .field .description {
    margin-bottom: 0.5rem;
  }

  .field input {
    padding: 0.5rem;
    border: 1px solid #ccc;
    border-radius: 0.25rem;
    font-size: 1rem;
  }
</style>
