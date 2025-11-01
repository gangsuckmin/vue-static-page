<template>
  <div class="page">
    <Lantern v-for="n in 6" :key="n" :delay="n * 1.2" color="var(--red)" :left="(n*12)%88" />
    <Lantern v-for="n in 6" :key="'b'+n" :delay="n * 1.1" color="var(--blue)" :left="(n*9)%80" reverse />
  </div>
</template>

<script setup lang="ts">
import { computed, defineComponent } from 'vue'

// Local Lantern component used in the template above
const Lantern = defineComponent({
  name: 'Lantern',
  props: {
    delay: { type: Number, default: 0 },
    left: { type: Number, default: 50 },
    color: { type: String, default: 'var(--red)' },
    reverse: { type: Boolean, default: false }
  },
  setup(props) {
    const lanternStyle = computed(() => ({
      '--delay': `${props.delay}s`,
      left: `${props.left}%`,
      '--lantern-color': props.color as string
    }) as Record<string, string>)

    return { lanternStyle, reverse: props.reverse }
  },
  template: `
    <div class='lantern' :style="lanternStyle" :class="{ reverse }">
      <div class='lantern__body'>
        <div class='lantern__top'></div>
        <div class='lantern__paper'></div>
        <div class='lantern__bottom'></div>
      </div>
      <div class='lantern__string'></div>
    </div>
  `
})
</script>

<style scoped>
:root {
  --red: #c1121f;
  --blue: #1e40ff;
  --ease: cubic-bezier(.25,.7,.2,1);
  --lantern-color: var(--red);
  --delay: 0s;
}

.page {
  position: relative;
  min-height: 100vh;
  background: radial-gradient(800px at 80% -10%, #1b2450, #0b1020);
}

.lantern {
  position: absolute;
  bottom: -10px;
  animation: float 16s var(--ease) infinite;
  animation-delay: var(--delay);
}
.lantern__body { width: 36px; height: 54px; background: var(--lantern-color); }

@keyframes float {
  0% { transform: translateY(0); opacity: 0; }
  50% { transform: translateY(-50vh); opacity: 1; }
  100% { transform: translateY(-100vh); opacity: 0; }
}
</style>
