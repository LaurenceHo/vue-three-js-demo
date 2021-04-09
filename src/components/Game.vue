<template>
  <div ref="threeRenderer"></div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import * as THREE from "three";

export default defineComponent({
  // Reference https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene
  mounted() {
    const threeRenderer: HTMLElement = this.$refs.threeRenderer as HTMLElement;

    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );

    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    threeRenderer.appendChild(renderer.domElement);

    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    const cube = new THREE.Mesh(geometry, material);
    scene.add(cube);

    camera.position.z = 5;

    function animate() {
      cube.rotation.x += 0.01;
      cube.rotation.y += 0.01;

      requestAnimationFrame(animate);
      renderer.render(scene, camera);
    }
    animate();
  },
});
</script>

<style scoped></style>
