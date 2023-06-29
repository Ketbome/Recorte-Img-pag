<script lang="ts">
  import "two-up-element";
  import { originalImage, modifiedImage } from "./store";

  let processingImage = true;
  let tries = 0;
  let intervalId: any;

  $: {
    if (processingImage) {
      clearInterval(intervalId);
      intervalId = setInterval(() => {
        tries++;
      }, 1000);
    }
  }
</script>

<two-up>
  <img src={$originalImage} alt="Imagen original" />
  <img
    on:load={() => (processingImage = false)}
    on:error={() => (processingImage = true)}
    src={`${$modifiedImage}&t=${tries}`}
    alt="Imagen Modificada"
  />
</two-up>

<a
  download
  href={$modifiedImage}
  class="block bg-blue-500 hover:bg-blue-700 w-full text-xl text-center items-center font-bold text-white rounded-full px-4 py-2"
>
  Descargar Imagen sin Fondo
</a>
