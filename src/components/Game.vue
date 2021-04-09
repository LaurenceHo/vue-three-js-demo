<template>
  <div>
    <div id="world" ref="threeRenderer" />
    <button @click="jump">Jump</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import * as THREE from "three";

export default defineComponent({
  name: "Game",

  setup() {
    const isJumping = false;
    const bounceValue = 0;
    const snowballOriginalPosition = 2.03;
    const sceneWidth = window.innerWidth;
    const sceneHeight = window.innerHeight - 80;
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      60,
      sceneWidth / sceneHeight,
      0.1,
      1000
    );
    // Set the position of the camera
    camera.position.y = 2.5;
    camera.position.z = 6.5;
    const renderer = new THREE.WebGLRenderer({
      alpha: true, //renderer with transparent backdrop
      antialias: true, // Activate the anti-aliasing
    });
    const snowball: any = {};
    const ground: any = {};

    return {
      isJumping,
      bounceValue,
      scene,
      camera,
      sceneWidth,
      sceneHeight,
      renderer,
      snowball,
      ground,
      snowballOriginalPosition,
    };
  },

  mounted() {
    this.initScene();
    this.initLight();
    this.renderGround();
    this.renderSnowball();
    this.animate();
  },

  methods: {
    initScene() {
      // Reference https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene
      const threeRenderer: HTMLElement = this.$refs
        .threeRenderer as HTMLElement;
      this.scene.fog = new THREE.FogExp2(0xf0fff0, 0.14);

      this.renderer.setSize(this.sceneWidth, this.sceneHeight);
      this.renderer.shadowMap.enabled = true; //enable shadow
      this.renderer.shadowMap.type = THREE.PCFSoftShadowMap;

      threeRenderer.appendChild(this.renderer.domElement);
    },

    initLight() {
      // Ref: https://threejs.org/docs/?q=HemisphereLight#api/en/lights/HemisphereLight
      const hemisphereLight = new THREE.HemisphereLight(
        0xfffafa,
        0x000000,
        0.9
      );
      const light = new THREE.DirectionalLight(0xcdc1c5, 0.9);
      // Set the direction of the light
      light.position.set(12, 6, -7);
      light.castShadow = true;
      this.scene.add(hemisphereLight);
      this.scene.add(light);
    },

    renderSnowball() {
      // Ref: https://threejs.org/docs/#api/en/geometries/SphereGeometry
      const geometry = new THREE.SphereGeometry(
        0.2,
        8,
        6,
        0,
        Math.PI * 2,
        0,
        Math.PI
      );
      // Ref: https://threejs.org/docs/index.html#api/en/materials/MeshStandardMaterial
      const material = new THREE.MeshStandardMaterial({
        color: 0xe5f2f2,
        flatShading: true,
      });
      this.snowball = new THREE.Mesh(geometry, material);
      this.snowball.receiveShadow = true;
      this.snowball.castShadow = true;
      this.snowball.position.y = this.snowballOriginalPosition;
      this.snowball.position.z = 4.8;
      this.scene.add(this.snowball);
    },

    renderGround() {
      // Draw another big SphereGeometry as ground
      const geometry = new THREE.SphereGeometry(
        26,
        40,
        40,
        0,
        Math.PI * 2,
        0,
        Math.PI
      );
      const material = new THREE.MeshStandardMaterial({
        color: 0x59332e,
        flatShading: true,
      });
      this.ground = new THREE.Mesh(geometry, material);
      this.ground.receiveShadow = true;
      // Adjust y position, make sure it's close to snowball
      this.ground.position.y = -23.99;
      this.ground.position.z = 2;
      this.scene.add(this.ground);
    },

    animate() {
      this.snowball.rotation.x += -0.08;
      //Make ground rolling, it will be looking like snowball rolling on it
      this.ground.rotation.x += 0.008;
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },

    jump() {
      if (!this.isJumping) {
        this.bounceValue = 0.8;
        this.isJumping = true;

        // It makes snowball jump
        this.snowball.position.y += this.bounceValue;
        setTimeout(() => {
          this.snowball.position.y = this.snowballOriginalPosition;
          this.isJumping = false;
          this.bounceValue = 0;
        }, 500);
      }
    },
  },
});
</script>

<style scoped>
#world {
  background: linear-gradient(#e4e0ba, #f7d9aa);
}

button {
  margin-top: 10px;
  height: 40px;
  padding: 16px;
  background-color: #3f82c3;
  border-color: #3f82c3;
  align-items: center;
  border-radius: 8px;
  display: inline-flex;
  border-style: none;
  font-weight: 500;
  font-size: 1rem;
  color: #fff;
}
</style>
