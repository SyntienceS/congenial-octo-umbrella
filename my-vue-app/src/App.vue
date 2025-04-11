# needs fixing
<template>
    <div>
      <h2>Koka sijas slodzes aprēķins</h2>
      <label>Sijas garums (L, m): <input type="number" v-model.number="L"></label>
      <label>Sijas platums (B, m): <input type="number" v-model.number="B"></label>
      <label>Sijas augstums (H, m): <input type="number" v-model.number="H"></label>
      <label>Stiprība (f, KPa): <input type="number" v-model.number="f"></label>
  
      <h3>Rezultāts:</h3>
      <p>W (Pretestības moments): {{ W.toFixed(4) }} m³</p>
      <p>q (Izkliedētā slodze): {{ q.toFixed(4) }} KN/m</p>
  
      <h3>3D Vizualizācija</h3>
      <canvas ref="canvas"></canvas>
    </div>
  </template>
  
  <script>
  import * as THREE from "three";
  
  export default {
    data() {
      return {
        L: 6,
        B: 0.1,
        H: 0.3,
        f: 20000,
        scene: null,
        camera: null,
        renderer: null,
        beam: null
      };
    },
    computed: {
      W() {
        return (this.B * Math.pow(this.H, 2)) / 6;
      },
      q() {
        return 8 * this.W * (this.f / Math.pow(this.L, 2));
      }
    },
    watch: {
      L: "updateBeam",
      B: "updateBeam",
      H: "updateBeam"
    },
    mounted() {
      this.initScene();
    },
    methods: {
      initScene() {
        this.scene = new THREE.Scene();
        this.camera = new THREE.PerspectiveCamera(75, 2, 0.1, 1000);
        this.camera.position.z = 5;
  
        this.renderer = new THREE.WebGLRenderer({ canvas: this.$refs.canvas });
        this.renderer.setSize(400, 200);
  
        const geometry = new THREE.BoxGeometry(this.L, this.H, this.B);
        const material = new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true });
  
        this.beam = new THREE.Mesh(geometry, material);
        this.scene.add(this.beam);
  
        this.animate();
      },
      updateBeam() {
        this.scene.remove(this.beam);
        const geometry = new THREE.BoxGeometry(this.L, this.H, this.B);
        this.beam = new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({ color: 0x00ff00, wireframe: true }));
        this.scene.add(this.beam);
      },
      animate() {
        requestAnimationFrame(this.animate);
        this.renderer.render(this.scene, this.camera);
      }
    }
  };
  </script>