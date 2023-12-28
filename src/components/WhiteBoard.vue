<template>
  <canvas ref="canvas"></canvas>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

const canvas = ref<HTMLCanvasElement | null>(null)
const context = ref<CanvasRenderingContext2D | null>()
const isDrawing = ref(false)
const startPos = ref({
  startX: 0,
  startY: 0,
})

const initCanvas = () => {
  if (canvas.value) {
    const clientW = window.innerWidth;
    const clientH = window.innerHeight;
    const devicePixelRatio = window.devicePixelRatio || 1;
    canvas.value.width = clientW * devicePixelRatio;
    canvas.value.height = clientH * devicePixelRatio;
    canvas.value.style.width = `${clientW}px`;
    canvas.value.style.height = `${clientH}px`;

    const gridSize = 50;
    if (context.value) {
      context.value.fillStyle = "rgba(0, 0, 0, .2)";
      for (let i = 0; i * gridSize < canvas.value?.width! - 40; i++) {
        for (var j = 0; j * gridSize < canvas.value?.height! - 40; j++) {
          if (i > 0 && j > 0) {
            context.value.beginPath();
            context.value.rect(i * gridSize, j * gridSize, 2 * window.devicePixelRatio, 2 * window.devicePixelRatio);
            context.value.fill();
            context.value.closePath();
          }
        }
      }
    }
  }
}

const startDrawing = (event: MouseEvent) => {
  startPos.value = {
    startX: event.clientX,
    startY: event.clientY
  }
  isDrawing.value = true;

  // isDrawing.value = true;
  // startX.value = event.clientX;
  // startY.value = event.clientY;

  if (context.value) {
    context.value.beginPath();
    context.value.moveTo(event.clientX * window.devicePixelRatio, event.clientY * window.devicePixelRatio);
  }
}

const draw = (event: MouseEvent) => {
  if (!isDrawing.value) {
    return;
  }

  if (context.value) {
    
    context.value.lineWidth = 20;
    context.value.lineCap = "round";
    context.value.lineJoin = "round";
    context.value.strokeStyle = '#d4f713';
    context.value.lineTo(event.clientX * window.devicePixelRatio, event.clientY * window.devicePixelRatio);
    // context.value.quadraticCurveTo(100, 25, 150, 50);
    context.value.stroke();
  }
}

const endDrawing = () => {
  isDrawing.value = false;
  context.value?.stroke();
  context.value?.beginPath();
}


onMounted(() => {
  context.value = canvas.value.getContext('2d')
  initCanvas()
  canvas.value?.addEventListener("mousedown", startDrawing);
  canvas.value?.addEventListener("mousemove", draw);
  canvas.value?.addEventListener("mouseup", endDrawing);

})
</script>
