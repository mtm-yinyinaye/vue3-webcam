<template>
  <img :src="src" :alt="alt" :class="className" @click="onClick" />
</template>

<script setup lang="ts">
import { defineProps, defineEmits } from "vue";

type Props = {
  src: string;
  alt?: string;
  className?: string;
  onClick?: void;
};
const props = defineProps<Props>();
</script>
