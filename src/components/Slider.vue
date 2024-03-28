<script setup>
import { computed, ref, onMounted, onUnmounted } from "vue";
const props = defineProps({
  items: {
    type: Array,
    default: [],
  },
  itemsToShow: Number,
  infinity: Boolean,
  animationDuration: Number,
  isFadable: Boolean,
  isScrollable: Boolean,
});

const slides = ref(JSON.parse(JSON.stringify(props.items)));
const width = ref(0);
const elementToMeasure = ref(null);
const index = ref(0);

const style = computed(() => ({
  "--width": `${100 / props.itemsToShow}%`,
  "--translate": props.isScrollable
    ? `-${
        index.value * (width.value / props.itemsToShow - 10) + index.value * 10
      }px`
    : "none",
  "--animation-duration": `${props.animationDuration || 0.3}s`,
}));

const nextSlide = () => {
  if (props.infinity) slides.value.push(slides.value[index.value]);
  index.value++;
  if (
    index.value - 1 == props.items.length - props.itemsToShow &&
    !props.infinity
  ) {
    index.value = 0;
  }
};

const preSlide = () => {
  if (index.value > 0) {
    index.value--;
  }
};

const setSlide = (i) => {
  const len = props.items.length;
  index.value = ((i % len) + len) % len;
};

const getDocumentWidth = () => {
  width.value = elementToMeasure.value.clientWidth;
};

defineExpose({
  nextSlide,
  preSlide,
  setSlide,
});

onMounted(() => {
  getDocumentWidth();
  window.addEventListener("resize", getDocumentWidth);
});

onUnmounted(() => {
  window.removeEventListener("resize", getDocumentWidth);
});
</script>

<template>
  <div class="slider" :style="style">
    <div class="slider__nav" @click="preSlide">
      <img src="@/assets/img/back-arrow.png" />
    </div>
    <div class="slider__inner" ref="elementToMeasure">
      <div class="slider__track">
        <template v-for="(slide, i) in slides" :key="i">
          <Transition :name="isFadable ? 'fade' : ''">
            <div class="slider__item" v-if="isScrollable || i > index">
              <img v-if="slide.type === 'image'" :src="slide.src" />
              <iframe
                v-else-if="slide.type === 'iframe'"
                :src="slide.src"
                frameborder="0"
                allowfullscreen
              ></iframe>
              <video v-else-if="slide.type === 'video'" controls>
                <source :src="slide.src" type="video/mp4" />
              </video>
            </div>
          </Transition>
        </template>
      </div>
    </div>
    <div class="slider__nav slider__nav--right" @click="nextSlide">
      <img src="@/assets/img/forward-arrow.png" />
    </div>
  </div>
</template>

<style scoped>
.transition {
  width: 100%;
  background: black;
}
.slider {
  width: 100%;
  position: relative;
}
.slider__track {
  transform: translateX(var(--translate));
  transition: all var(--animation-duration);
  width: 100%;
  display: flex;
  flex: 0 0 100%;
  gap: 10px;
}
.slider__nav {
  position: absolute;
  top: 0px;
  bottom: 0px;
  transform: translateY(50%);
  cursor: pointer;
}
.slider__nav--right {
  right: 0px;
}
.slider__item {
  flex: 0 0 calc(var(--width) - 10px);
}
.slider__item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 5px;
}
.slider__inner {
  margin: 50px;
  overflow: hidden;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity var(--animation-duration);
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
