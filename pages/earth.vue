<template>
  <div ref="earthContainer" class="earth-container"></div>
</template>

<script>
import * as THREE from "three";

export default {
  mounted() {
    this.initThreeJS();
  },
  methods: {
    async initThreeJS() {
      const { OrbitControls } = await import(
        "three/examples/jsm/controls/OrbitControls"
      );

      // Dynamically import EffectComposer, RenderPass, and UnrealBloomPass
      const { EffectComposer } = await import(
        "three/examples/jsm/postprocessing/EffectComposer"
      );
      const { RenderPass } = await import(
        "three/examples/jsm/postprocessing/RenderPass"
      );
      const { UnrealBloomPass } = await import(
        "three/examples/jsm/postprocessing/UnrealBloomPass"
      );

      // Scene
      const scene = new THREE.Scene();

      // Camera
      const camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );
      camera.position.z = 5;

      // Renderer
      const renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(window.innerWidth, window.innerHeight);
      this.$refs.earthContainer.appendChild(renderer.domElement);

      // Composer for Bloom Effect
      const composer = new EffectComposer(renderer);
      const renderPass = new RenderPass(scene, camera);
      composer.addPass(renderPass);

      const bloomPass = new UnrealBloomPass(
        new THREE.Vector2(window.innerWidth, window.innerHeight),
        1.5, // strength
        0.4, // radius
        0.85 // threshold
      );
      composer.addPass(bloomPass);

      // Earth Geometry
      const geometry = new THREE.SphereGeometry(1, 32, 32);

      // Earth Material
      const textureLoader = new THREE.TextureLoader();
      const earthTexture = textureLoader.load(require("~/static/earth.jpg"));
      const material = new THREE.MeshBasicMaterial({ map: earthTexture });

      // Earth Mesh
      const earth = new THREE.Mesh(geometry, material);
      scene.add(earth);

      // Orbit Controls
      const controls = new OrbitControls(camera, renderer.domElement);

      let stars;

      const addStars = () => {
        const starGeometry = new THREE.BufferGeometry();
        const starMaterial = new THREE.PointsMaterial({
          color: 0xffffff,
          size: 0.1,
          transparent: true,
          opacity: 0.8,
        });

        const starVertices = [];
        for (let i = 0; i < 10000; i++) {
          const x = THREE.MathUtils.randFloatSpread(2000);
          const y = THREE.MathUtils.randFloatSpread(2000);
          const z = THREE.MathUtils.randFloatSpread(2000);
          starVertices.push(x, y, z);
        }

        starGeometry.setAttribute(
          "position",
          new THREE.Float32BufferAttribute(starVertices, 3)
        );

        stars = new THREE.Points(starGeometry, starMaterial);
        scene.add(stars);
      };

      addStars();

      // Add circular line to represent the airplane orbiting the earth
      const addOrbitingLine = (
        rotationAxis1,
        rotationAngle1,
        rotationAxis2,
        rotationAngle2
      ) => {
        // Create a circular path
        const points = [];
        const radius = 1.2;
        for (let i = 0; i <= 64; i++) {
          const theta = (i / 64) * Math.PI * 2;
          points.push(
            new THREE.Vector3(
              radius * Math.cos(theta),
              0,
              radius * Math.sin(theta)
            )
          );
        }

        const curve = new THREE.CatmullRomCurve3(points);

        const tubeGeometry = new THREE.TubeGeometry(curve, 100, 0.02, 20, true);

        // Custom Shader Material for Glow Effect
        const tubeMaterial = new THREE.MeshBasicMaterial({
          color: 0xffffff,
          transparent: true,
          opacity: 1,
        });

        const tube = new THREE.Mesh(tubeGeometry, tubeMaterial);
        const quaternion1 = new THREE.Quaternion();
        const quaternion2 = new THREE.Quaternion();
        quaternion1.setFromAxisAngle(rotationAxis1, rotationAngle1);
        quaternion2.setFromAxisAngle(rotationAxis2, rotationAngle2);
        tube.applyQuaternion(quaternion1);
        tube.applyQuaternion(quaternion2);
        earth.add(tube); // Add the tube to the earth mesh

        const animateOrbit = () => {
          requestAnimationFrame(animateOrbit);
          tube.rotation.y += 0.001; // Rotate the entire tube to simulate orbiting
        };

        animateOrbit();
      };

      // Add first orbiting line
      addOrbitingLine(
        new THREE.Vector3(0, 1, 0),
        0,
        new THREE.Vector3(1, 0, 0),
        Math.PI / 4
      );

      // Add second orbiting line at a 45-degree angle to the first one
      addOrbitingLine(
        new THREE.Vector3(0, 1, 0),
        Math.PI / 2,
        new THREE.Vector3(1, 0, 0),
        -Math.PI / 4
      );

      // Animation
      const animate = () => {
        requestAnimationFrame(animate);
        earth.rotation.y += 0.001; // Rotate the earth along with the orbiting lines
        if (stars) {
          stars.rotation.y += 0.0005;
        }
        controls.update();
        composer.render(); // Use composer to render the scene with bloom effect
      };

      animate();

      // Handle window resize
      window.addEventListener("resize", () => {
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
        renderer.setSize(window.innerWidth, window.innerHeight);
        composer.setSize(window.innerWidth, window.innerHeight); // Update composer size
      });
    },
  },
};
</script>

<style scoped>
.earth-container {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
</style>
