<template>
  <div class="input-file-body">
    <input
      type="file"
      :accept="accept"
      class="input-file"
      @change="onChange"
    />
    <img src="@/assets/img/gallery.png" class="gallery_img" />
  </div>
</template>

<script setup lang="ts">
import { defineProps, defineEmits } from "vue";
const props = defineProps({
  accept: String,
});
interface Emits {
  (e: "change", event: any): void;
}
const emit = defineEmits<Emits>();

const onChange = (event: any) => {
  emit("change", event);
};
</script>

<style scoped>
.input-file {
  -webkit-appearance: none;
  appearance: none;
  position: absolute;
  z-index: 2;
  bottom: 5px;
  left: 15px;
  opacity: 0;
  width: 40px;
  height: 100%;
  cursor: pointer;
}
.gallery_img {
  position: absolute;
  bottom: 5px;
  left: 15px;
  width: 40px;
  height: 40px;
  cursor: pointer;
}
</style>
