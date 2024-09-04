<template>
  <main>
    <section class="h-screen grid place-content-center">
      <Header />
      <Hero3d />
    </section>
    <section class="section-content bg-[--white-ncy] relative z-10">
      <div class="row">
        <video
          class="mx-auto rounded-2xl w-full"
          src="/video-hero.mp4"
          autoplay
          muted
          loop
        ></video>
      </div>
    </section>
  </main>
</template>
<script setup>
import { animate, scroll } from "motion";

onMounted(() => {
  const video = document.querySelector("video");
  scroll(
    animate(video, {
      width: ["60%", "100%"],
      transform: ["translateY(0)", "translateY(-400px)"],
    })
  );
});
</script>