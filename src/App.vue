<script setup lang="ts">
import { ref } from "vue";
import Entity from "./components/Entity.vue";

const dragOver = function (e: DragEvent) {
  e.dataTransfer!.dropEffect = "move";
  e.preventDefault();
};

var data = [
  {
    name: "e0",
    height: 400,
    width: 300,
    left: 100,
    top: 200,
    value: "e0 的value数据",
    data: [{ name: "name", value: "222", type: "const" }],
  },
  {
    name: "e1",
    height: 400,
    width: 300,
    left: 800,
    top: 200,
    value: "e1的value数据",
    data: [
      {
        name: "name",
        value: "e0",
        type: "expr",
        compute: null,
        up: ["e0.data.name"],
      },
    ],
  },
  {
    name: "e2",
    height: 400,
    width: 300,
    left: 1200,
    top: 200,
    value: "e2的value数据22222222222",
    data: [
      {
        name: "name",
        value: "e0.data.name+e1.data.name",
        type: "expr",
        compute: null,
        up: ["e0.data.name", "e1.data.name"],
      },
    ],
  },
];

var entitys = ref(data);
</script>

<template>
  <div class="relative w-full h-full" @dragover="dragOver($event)">
    <template v-for="(e, idx) in entitys" :key="idx">
      <Entity v-model="entitys[idx]" />
    </template>
  </div>
</template>

<style>
#app {
  width: 100vw;
  height: 100vh;
}
</style>
