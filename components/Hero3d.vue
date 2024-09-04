<template>
  <div class="fixed top-0 left-0 canvas"></div>
</template>
<script setup>
import * as THREE from "three";
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { RGBELoader } from "three/addons/loaders/RGBELoader.js";
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";

gsap.registerPlugin(ScrollTrigger);

onMounted(() => {
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  );

  camera.position.z = 50;

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.encoding = THREE.sRGBEncoding;
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.querySelector(".canvas").append(renderer.domElement);

  // LIGHTS

  const light = new THREE.DirectionalLight(0xffffff, 10);
  light.position.set(0, 0, 80);
  light.target.position.set(0, 0, 0);
  scene.add(light.target);
  scene.add(light);

  // CONTROLS

  //   const controls = new OrbitControls(camera, renderer.domElement);
  //   controls.update();

  const hdrEquirect = new RGBELoader().load(
    "/empty_warehouse_01_1k.hdr",
    () => {
      hdrEquirect.mapping = THREE.EquirectangularReflectionMapping;
    }
  );

  const glassMaterial = new THREE.MeshPhysicalMaterial({
    transmission: 1,
    thickness: 3,
    roughness: 0.1,
    envMap: hdrEquirect,
  });

  new THREE.TextureLoader().load("/bg-hero-home.jpg", (texture) => {
    const bgGeometry = new THREE.BoxGeometry(300, 200);
    const bgMaterial = new THREE.MeshBasicMaterial({
      map: texture,
      color: 0x2d6651,
    });
    const bgMesh = new THREE.Mesh(bgGeometry, bgMaterial);
    bgMesh.position.z = -40;
    scene.add(bgMesh);
  });

  var model = null;

  const loader = new GLTFLoader();
  loader.load("/3d-models/logo-n-obj-2.glb", (gltf) => {
    model = gltf.scene;

    const scaleNumber = 8;
    model.scale.set(scaleNumber, scaleNumber, scaleNumber);

    model.traverse((o) => {
      if (o.isMesh) {
        o.material = glassMaterial;
      }
    });

    if (model) {
      gsap
        .timeline({
          scrollTrigger: {
            trigger: ".section-content",
            scrub: true,
          },
        })
        .to(model.position, {
          y: 100,
          duration: 2,
        });

      gsap
        .timeline({
          scrollTrigger: {
            trigger: ".section-content",
            scrub: true,
          },
        })
        .to(model.scale, {
          y: 3,
          x: 3,
          z: 3,
          duration: 2,
        });

      gsap
        .timeline({
          scrollTrigger: {
            trigger: ".section-content",
            scrub: true,
          },
        })
        .to(model.rotation, {
          z: -1,
          x: -1,
          duration: 2,
        });
    }

    scene.add(model);
  });

  loader.load("/3d-models/title-ncy.glb", (gltf) => {
    let text = gltf.scene;
    const scaleNumberText = 8;
    text.scale.set(scaleNumberText, scaleNumberText, scaleNumberText);
    text.traverse((o) => {
      if (o.isMesh) {
        o.material.color.setHex(0xfd6c43);
      }
    });
    text.position.z = -20;
    scene.add(text);
  });

  //   const helper = new THREE.DirectionalLightHelper(light, 5);
  //   scene.add(helper);

  let steps = 0.001;

  const animate = () => {
    requestAnimationFrame(animate);
    if (model) {
      model.rotation.y += steps;
      if (model.rotation.y > Math.PI / 5) {
        steps = -0.001;
      } else if (model.rotation.y < -Math.PI / 5) {
        steps = 0.001;
      }
    }

    renderer.render(scene, camera);
  };

  animate();
});
</script>