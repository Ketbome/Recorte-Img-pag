<script lang="ts">
  import { Cloudinary } from "@cloudinary/url-gen";
  import { backgroundRemoval } from "@cloudinary/url-gen/actions/effect";
  import { ImageStatus } from "./types.d";
  import { imageStatus, modifiedImage } from "./store";
  import Dropzone from "dropzone";
  import "dropzone/dist/dropzone.css";
  import { onMount } from "svelte";
  import { originalImage } from "./store";

  const cloudinary = new Cloudinary({
    cloud: {
      cloudName: "ketbome",
    },
    url: {
      secure: true,
    },
  });

  onMount(() => {
    const dropzone = new Dropzone("#dropzone", {
      uploadMultiple: false,
      acceptedFiles: ".jpg,.png,.webp",
      maxFiles: 1,
    });

    dropzone.on("sending", (file, xhr, formData) => {
      imageStatus.set(ImageStatus.UPLOADING);
      // aqui podemos añadir apiKey
      formData.append("upload_preset", "ml_default");
      formData.append("timestamp", Date.now() / 1000);
      formData.append("api_key", 222962456332937);
    });

    dropzone.on("success", (file, response) => {
      const { public_id: publicId, url: url } = response;
      console.log(response);
      const imageSinFondo = cloudinary
        .image(publicId)
        .effect(backgroundRemoval());
      console.log(imageSinFondo.toURL());
      imageStatus.set(ImageStatus.DONE);
      modifiedImage.set(imageSinFondo.toURL());
      originalImage.set(url);
    });

    dropzone.on("error", (file, response) => {
      imageStatus.set(ImageStatus.ERROR);
      console.log(response);
    });
  });
</script>

<form
  id="dropzone"
  class="shadow-2xl border-dashed border-2 border-gray-300 rounded-lg aspect-video w-full flex items-center justify-center flex-col"
  action="https://api.cloudinary.com/v1_1/dw0w4h3hg/image/upload"
>
  {#if $imageStatus == ImageStatus.READY}
    <button
      class="rounded-full pointer-events-none bg-blue-600 hover:bg-blue-800 rouded-full text-bold text-white text-cl px-6 py-4"
    >
      Carga imagen
    </button>
    <strong class="text-lg mt-4 text-gray-800"
      >También puedes arrastrar la imagen</strong
    >
  {/if}
  {#if $imageStatus == ImageStatus.UPLOADING}
    <strong class="text-lg mt-4 text-gray-800">Subiendo...</strong>
  {/if}
</form>
