<template>
  <canvas
    ref="canvasRef"
    class="rain-canvas"
    :width="canvasWidth"
    :height="canvasHeight"
  ></canvas>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from "vue";

// Canvas 引用和尺寸
const canvasRef = ref<HTMLCanvasElement | null>(null);
const canvasWidth = ref(window.innerWidth);
const canvasHeight = ref(window.innerHeight);

// 雨滴类
class Raindrop {
  x: number;
  y: number;
  length: number;
  speed: number;
  opacity: number;
  angle: number;
  thickness: number;

  constructor() {
    this.x = Math.random() * canvasWidth.value;
    this.y = Math.random() * canvasHeight.value - canvasHeight.value;

    // 根据屏幕大小调整雨滴属性
    const isMobile = window.innerWidth < 768;

    this.length = Math.random() * 15 + (isMobile ? 18 : 15); // 移动端更长
    this.speed = Math.random() * 4 + 2; // 稍快的速度
    this.opacity = Math.random() * 0.6 + (isMobile ? 0.4 : 0.3); // 移动端更明显
    this.angle = Math.PI / 8; // 更小的倾斜角度
    this.thickness = Math.random() * 0.8 + (isMobile ? 1.2 : 1); // 移动端更粗
  }

  update() {
    this.y += this.speed;
    this.x += Math.sin(this.angle) * this.speed * 0.5;

    // 重置雨滴位置
    if (this.y > canvasHeight.value) {
      this.y = -this.length;
      this.x = Math.random() * canvasWidth.value;
    }
    if (this.x > canvasWidth.value) {
      this.x = 0;
    }
  }

  draw(ctx: CanvasRenderingContext2D) {
    ctx.save();
    ctx.globalAlpha = this.opacity;

    // 创建柔和的渐变效果
    const gradient = ctx.createLinearGradient(
      this.x,
      this.y,
      this.x - Math.sin(this.angle) * this.length,
      this.y - Math.cos(this.angle) * this.length
    );
    gradient.addColorStop(0, "#A8D8EA"); // 柔和的浅蓝
    gradient.addColorStop(0.5, "#B8E6B8"); // 淡绿蓝色
    gradient.addColorStop(1, "#D4E6F1"); // 很淡的蓝色

    ctx.strokeStyle = gradient;
    ctx.lineWidth = this.thickness;
    ctx.lineCap = "round";

    ctx.beginPath();
    ctx.moveTo(this.x, this.y);
    ctx.lineTo(
      this.x - Math.sin(this.angle) * this.length,
      this.y - Math.cos(this.angle) * this.length
    );
    ctx.stroke();
    ctx.restore();
  }
}

// 雨滴数组和动画控制
const raindrops: Raindrop[] = [];
let animationId: number | null = null;

// 初始化雨滴
const initRain = () => {
  // 根据屏幕大小调整雨滴密度，移动端也要有足够密度
  const isMobile = window.innerWidth < 768;
  const density = isMobile ? 6000 : 7000; // 移动端增加密度
  const dropCount = Math.floor(
    (canvasWidth.value * canvasHeight.value) / density
  );
  raindrops.length = 0;

  for (let i = 0; i < dropCount; i++) {
    raindrops.push(new Raindrop());
  }
};

// 动画循环
const animate = () => {
  const canvas = canvasRef.value;
  if (!canvas) return;

  const ctx = canvas.getContext("2d");
  if (!ctx) return;

  // 清除画布
  ctx.clearRect(0, 0, canvasWidth.value, canvasHeight.value);

  // 更新和绘制雨滴
  raindrops.forEach((drop) => {
    drop.update();
    drop.draw(ctx);
  });

  animationId = requestAnimationFrame(animate);
};

// 窗口大小调整处理
const handleResize = () => {
  canvasWidth.value = window.innerWidth;
  canvasHeight.value = window.innerHeight;
  initRain();
};

// 组件挂载
onMounted(() => {
  initRain();
  animate();
  window.addEventListener("resize", handleResize);
});

// 组件卸载
onUnmounted(() => {
  if (animationId) {
    cancelAnimationFrame(animationId);
  }
  window.removeEventListener("resize", handleResize);
});
</script>

<style scoped>
.rain-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 1;
  opacity: 0.8; /* 柔和的整体透明度 */
  transition: opacity 0.3s ease;
}

/* 在加载时隐藏雨滴效果 */
.minimal-page:not(.loaded) .rain-canvas {
  opacity: 0;
}

/* 响应式优化 */
@media (max-width: 768px) {
  .rain-canvas {
    opacity: 0.7; /* 移动端也要明显 */
  }
}

@media (prefers-reduced-motion: reduce) {
  .rain-canvas {
    display: none; /* 为有动画敏感的用户隐藏 */
  }
}
</style>
