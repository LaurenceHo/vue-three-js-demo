<template>
  <div ref="threeRenderer"></div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import * as THREE from "three";

export default defineComponent({
  setup() {
    const snowballOriginalPosition = 2.03;
    const sceneWidth = window.innerWidth;
    const sceneHeight = window.innerHeight;
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      60,
      sceneWidth / sceneHeight,
      0.1,
      1000
    );
    const renderer = new THREE.WebGLRenderer({
      alpha: true, //renderer with transparent backdrop
      antialias: true, // Activate the anti-aliasing
    });
    const cube: any = {};

    return {
      scene,
      camera,
      sceneWidth,
      sceneHeight,
      renderer,
      cube,
      snowballOriginalPosition,
    };
  },

  mounted() {
    this.initScene();
    this.renderCube();
    this.animate();
  },

  methods: {
    initScene() {
      // Reference https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene
      const threeRenderer: HTMLElement = this.$refs
        .threeRenderer as HTMLElement;
      this.renderer.setSize(this.sceneWidth, this.sceneHeight);
      threeRenderer.appendChild(this.renderer.domElement);
    },

    renderCube() {
      const geometry = new THREE.BoxGeometry();
      const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
      this.cube = new THREE.Mesh(geometry, material);
      this.scene.add(this.cube);
      this.camera.position.z = 5;
    },

    animate() {
      this.cube.rotation.x += 0.01;
      this.cube.rotation.y += 0.01;

      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
  },
});
</script>

<style scoped></style>
