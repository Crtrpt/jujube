<script setup lang="ts">
import { ref } from "vue";
import Entity from "./components/Entity.vue";
import Interpreter from "./3rd/interpreter";

const dragOver = function (e: DragEvent) {
  e.dataTransfer!.dropEffect = "move";
  e.preventDefault();
};

var data = [
  {
    name: "e0",
    height: 100,
    width: 100,
    left: 100,
    top: 200,
    value: {
      value: "100",
      expr: "",
    },
  },
  {
    name: "e1",
    height: 100,
    width: 100,
    left: 300,
    top: 200,
    value: {
      value: "",
      expr: "e1.value",
    },
  },
  {
    name: "e2",
    height: 100,
    width: 100,
    left: 600,
    top: 200,
    value: {
      value: "",
      expr: "e1.value",
    },
  },
];

var interpreter = new Interpreter("e1.top+e0.top", function (
  interpreter: any,
  globalObject: any
) {
  data.forEach((e) => {
    var entity = interpreter.nativeToPseudo(e);
    interpreter.setProperty(globalObject, e.name, entity);
  });
});
interpreter.run();
console.log(interpreter.value);

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
