<template>
  <q-page>
    <canvas ref="bjsCanvas" class="full-width full-height" />
  </q-page>
</template>

<script lang="ts">
import { Engine } from '@babylonjs/core';
import {
  defineComponent,
  onMounted,
  onUnmounted,
  ref
} from '@vue/composition-api';

import { SpaceInvadersScene } from 'src/scenes/SpaceInvaders.scene';

export default defineComponent({
  name: 'PageIndex',
  setup() {
    const bjsCanvas = ref<HTMLCanvasElement | null>(null);
    let engine: Engine;
    onMounted(() => {
      if (bjsCanvas?.value) {
        const spaceInvadersScene = new SpaceInvadersScene(bjsCanvas.value);
        engine = spaceInvadersScene.getEngine();
        spaceInvadersScene.initScene();
        spaceInvadersScene.startScene();

        window.addEventListener('resize', onWindowResize);
      }
    });

    onUnmounted(() => {
      cleanup();
    });

    const cleanup = () => {
      window.removeEventListener('resize', onWindowResize);
    };

    const onWindowResize = () => {
      engine.resize();
    };

    return {
      bjsCanvas
    };
  }
});
</script>
