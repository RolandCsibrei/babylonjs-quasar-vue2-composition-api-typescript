<template>
  <q-page>
    <canvas ref="bjsCanvas" />
  </q-page>
</template>

<script lang="ts">
import {
  defineComponent,
  onMounted,
  onUnmounted,
  ref
} from '@vue/composition-api';
import {
  Color4,
  Engine,
  FreeCamera,
  HemisphericLight,
  MeshBuilder,
  Scene,
  Vector3
} from '@babylonjs/core';

const BJS_CANVAS_WIDTH = 500;
const BJS_CANVAS_HEIGHT = 500;
const BJS_CANVAS_ZINDEX = '9';

export default defineComponent({
  name: 'PageIndex',
  setup() {
    let engine: Engine;

    // the variable name MUST match the ref value used in the html templte above
    const bjsCanvas = ref<HTMLCanvasElement | null>(null);

    onMounted(() => {
      if (bjsCanvas?.value) {
        // you have to set the width on the canvas object
        // setting the width and height using css or canvas.style
        // only stretches the canvas to the desired dimensions
        bjsCanvas.value.width = BJS_CANVAS_WIDTH;
        bjsCanvas.value.height = BJS_CANVAS_HEIGHT;
        bjsCanvas.value.style.zIndex = BJS_CANVAS_ZINDEX;

        // do not forget to use the .value property on the ref object
        // everywhere you need to access the HTMLCanvasElement
        const engine = new Engine(bjsCanvas.value);
        const scene = new Scene(engine);

        // create a scene
        createScene(scene, bjsCanvas.value);

        // the render loop is actually rendering the scene
        setupRenderLoop(engine, scene);

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

    const setupRenderLoop = (engine: Engine, scene: Scene) => {
      engine.runRenderLoop(() => {
        scene.render();
      });
    };

    const createScene = (scene: Scene, canvas: HTMLCanvasElement) => {
      // setting up a basic scene
      const camera = new FreeCamera('camera1', new Vector3(0, 5, -10), scene);
      camera.setTarget(Vector3.Zero());
      camera.attachControl(canvas, true);

      scene.clearColor = new Color4(0.3, 0.2, 0.7, 1);

      const light = new HemisphericLight('light', new Vector3(0, 1, 0), scene);
      light.intensity = 0.7;

      const sphere = MeshBuilder.CreateSphere(
        'sphere',
        { diameter: 2, segments: 32 },
        scene
      );
      sphere.position.y = 1;

      MeshBuilder.CreateGround('ground', { width: 6, height: 6 }, scene);
    };

    // you need to return the ref to the babylonJS canvas
    return {
      bjsCanvas
    };
  }
});
</script>
