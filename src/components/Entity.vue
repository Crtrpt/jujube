<script setup lang="ts">
import { ref, watch, useAttrs } from "vue";
const attrs = useAttrs();

const topText = ref(0);

const props = defineProps({
  width: Number,
  height: Number,
  top: Number,
  left: Number,
  name: String,
});

watch(
  () => props,
  () => {}
);

const emit = defineEmits(["update:modelValue"]);

const drag = function (e: DragEvent) {
  e.dataTransfer!.dropEffect = "none";
  topText.value = e.offsetX;
};

var x1 = 0;
var y1 = 0;
const dragstart = function (e: DragEvent) {
  x1 = e.offsetX;
  y1 = e.offsetY;
};
var x2 = 0;
var y2 = 0;
const dragend = function (e: DragEvent) {
  x2 = e.offsetX;
  y2 = e.offsetY;

  var offsetx = x2 - x1;
  var offsety = y2 - y1;
  topText.value = e.offsetX;
  emit("update:modelValue", {
    ...attrs.modelValue,
    ...{
      left: attrs.modelValue.left + offsetx,
      top: attrs.modelValue.top + offsety,
    },
  });
};
</script>

<template>
  <div
    draggable="true"
    @dragstart="dragstart"
    @drag="drag"
    @dragend="dragend"
    class="absolute border flex flex-col m-1 p-1"
    :style="{
      width: $attrs.modelValue.width + 'px',
      height: $attrs.modelValue.height + 'px',
      top: $attrs.modelValue.top + 'px',
      left: $attrs.modelValue.left + 'px',
    }"
  >
    name:{{ $attrs.modelValue.name }} value:{{
      $attrs.modelValue.value.value || "-"
    }}
    <input class="border m-1" v-model="$attrs.modelValue.value.value" />
    <input class="border m-1" v-model="$attrs.modelValue.value.expr" />
  </div>
</template>

<style scoped>
</style>
