<script setup>
import Slider from "@/components/Slider.vue";
import { ref } from "vue";

const slider = ref(null);

const next = () => slider.value.nextSlide();
const prev = () => slider.value.preSlide();
const set = (index) => slider.value.setSlide(index);

const slides = [
  { type: "image", src: "https://picsum.photos/id/234/200/300" },
  { type: "image", src: "https://picsum.photos/id/235/200/300" },
  { type: "image", src: "https://picsum.photos/id/238/200/300" },
  { type: "image", src: "https://picsum.photos/id/239/200/300" },
  { type: "image", src: "https://picsum.photos/id/240/200/300" },
  { type: "image", src: "https://picsum.photos/id/241/200/300" },
  { type: "image", src: "https://picsum.photos/id/242/200/300" },
  { type: "image", src: "https://picsum.photos/id/243/200/300" },
];
</script>

<template>
  <Slider
    ref="slider"
    :items="slides"
    :itemsToShow="4"
    infinity
    is-scrollable
    :animation-duration="0.9"
  />
</template>
<style></style>
