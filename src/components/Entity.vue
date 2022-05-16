<script setup lang="ts">
import { ref, watch, useAttrs } from "vue";
import emitter from "../emitter";
import evoluter from "../evoluter";

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

var PseudoEntity = evoluter.nativeToPseudo(attrs.modelValue);
evoluter.setProperty(
  evoluter.globalObject,
  attrs.modelValue.name,
  PseudoEntity
);

const change = function (
  e: Event,
  entity: any,
  k: String,
  v: Object,
  data: any,
  idx: any
) {
  var code = k + "=" + v;

  evoluter.appendCode(code);
  evoluter.run();
  console.log("虚拟环境中获取");
  entity.data[idx].value = data.value;

  var NativeEntity = evoluter.pseudoToNative(PseudoEntity);
  NativeEntity.data[idx].value = data.value;
  emit("update:modelValue", NativeEntity);
};
</script>

<template>
  <div
    class="absolute border-2 border-black flex flex-col m-1 p-1"
    :style="{
      width: $attrs.modelValue.width + 'px',
      height: $attrs.modelValue.height + 'px',
      top: $attrs.modelValue.top + 'px',
      left: $attrs.modelValue.left + 'px',
    }"
  >
    name:{{ $attrs.modelValue.name }} value:{{ $attrs.modelValue.value }}
    <div class="flex" v-for="(d, idx) in $attrs.modelValue.data" :key="d.name">
      <div class="">
        <input class="border border-black p-1 m-1 w-full" v-model="d.name" />
        <input
          class="border border-black p-1 m-1 w-full"
          v-model="d.value"
          @change="
            change(
              $event,
              $attrs.modelValue,
              $attrs.modelValue.name + '.data[' + idx + '].compute',
              d.value,
              d,
              idx
            )
          "
        />

        <div class="bg-green-400 border p-1 m-1" v-if="d.type == 'const'">
          {{ d.value }}
        </div>
        <div class="bg-yellow-400 border p-1 m-1" v-if="d.type == 'expr'">
          {{ d.compute }}
        </div>
        <a class="whitespace-nowrap border p-1 m-1" href="#">expr</a>
        <div>
          <div class="border p-1 m-1" v-for="u in d.up" :key="u">
            {{ u }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script lang="ts">
export default {
  mounted() {},
  setup() {},
};
</script>

<style scoped>
</style>
