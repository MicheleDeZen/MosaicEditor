<script setup>
    import {Scene, PerspectiveCamera, WebGLRenderer, Mesh, SphereGeometry, MeshBasicMaterial} from 'three'
    import { computed, ref, onMounted, watch } from 'vue';
    import { useWindowSize } from '@vueuse/core';

    let renderer = WebGLRenderer;
    let camera = PerspectiveCamera;
    const experience = ref(null);

    const scene = new Scene();

    //Get window size using vueuse core 'useWindowSize'
    const {width, height} = useWindowSize();

    //Compute the aspect ration with width and height
    const aspectRatio = computed(()=> width.value / height.value)

    //Create 2 functions to update camera and renderer every time the canvas is resized
    function updateRenderer() {
      renderer.setSize(width.value, height.value);
      renderer.setPixelRatio(window.devicePixelRatio);
    }
    function updateCamera() {
      camera.aspect = aspectRatio.value;
      camera.updateProjectionMatrix();
    }
    // Use vue to watch for the change of aspect ratio and update camera and renderer
    watch(aspectRatio,updateRenderer);
    watch(aspectRatio,updateCamera);

    camera = new PerspectiveCamera(
      45, //fov
      aspectRatio.value, 
      0.1, //Near limit
      1000  //Far limit
    );

    //By default all objects added to the scene are placed in the center
    //Moving the camera away lets you see the sphere
    camera.position.z = 5;

    scene.add(camera);

    //Objects are meshes composed by the geometry and the material
    const sphere = new Mesh(
      new SphereGeometry(1, 20, 20),
      new MeshBasicMaterial({ color: 0x008080}),
    );

    scene.add(sphere);

    //The draw loop lets the application redraw every object in frames
    const loop = () => {
      renderer.render(scene,camera);

      requestAnimationFrame(loop);
    }

    onMounted(() => {
      //The renderer gets created only when the component gets mounted
      renderer = new WebGLRenderer({
      canvas: experience.value
      });
      renderer.setSize(width.value, height.value);
      loop();
    })

</script>

<template>
  <canvas ref="experience"></canvas>
</template>

<style scoped>

</style>
