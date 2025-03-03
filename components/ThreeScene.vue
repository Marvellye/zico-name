<template>
  <div ref="container" class="canvas-container"></div>
</template>

<script setup>
import * as THREE from 'three';
import { onMounted, onBeforeUnmount, ref } from 'vue';

const container = ref(null);
let scene, camera, renderer, objects = [];
let animationFrameId;

onMounted(() => {
  initThree();
  animate();
  window.addEventListener('resize', onWindowResize);
});

onBeforeUnmount(() => {
  window.removeEventListener('resize', onWindowResize);
  cancelAnimationFrame(animationFrameId);
  
  // Clean up resources
  objects.forEach(obj => {
    if (obj.geometry) obj.geometry.dispose();
    if (obj.material) obj.material.dispose();
  });
  
  if (renderer) renderer.dispose();
});

function initThree() {
  // Create scene
  scene = new THREE.Scene();
  
  // Create camera
  camera = new THREE.PerspectiveCamera(
    75,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
  );
  camera.position.z = 5;
  
  // Create renderer
  renderer = new THREE.WebGLRenderer({ 
    antialias: true,
    alpha: true
  });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  container.value.appendChild(renderer.domElement);
  
  // Add ambient light
  const ambientLight = new THREE.AmbientLight(0x404040, 2);
  scene.add(ambientLight);
  
  // Add directional light
  const directionalLight = new THREE.DirectionalLight(0x4169e1, 2);
  directionalLight.position.set(1, 1, 1);
  scene.add(directionalLight);
  
  // Create objects
  createObjects();
}

function createObjects() {
  // Create different geometric shapes
  const geometries = [
    new THREE.IcosahedronGeometry(0.5, 0),
    new THREE.OctahedronGeometry(0.5, 0),
    new THREE.TetrahedronGeometry(0.5, 0),
    new THREE.SphereGeometry(0.3, 32, 32),
    new THREE.TorusGeometry(0.3, 0.1, 16, 100)
  ];
  
  // Materials with glass-like appearance
  const materials = [
    new THREE.MeshPhysicalMaterial({
      color: 0x4169e1,
      metalness: 0.2,
      roughness: 0.1,
      transmission: 0.9,
      transparent: true,
      opacity: 0.6
    }),
    new THREE.MeshPhysicalMaterial({
      color: 0x64ffda,
      metalness: 0.1,
      roughness: 0.2,
      transmission: 0.8,
      transparent: true,
      opacity: 0.7
    }),
    new THREE.MeshStandardMaterial({
      color: 0x0a192f,
      metalness: 0.7,
      roughness: 0.2,
      transparent: true,
      opacity: 0.8
    })
  ];
  
  // Create multiple objects and position them randomly
  for (let i = 0; i < 15; i++) {
    const geometry = geometries[Math.floor(Math.random() * geometries.length)];
    const material = materials[Math.floor(Math.random() * materials.length)];
    
    const mesh = new THREE.Mesh(geometry, material);
    
    // Random position
    mesh.position.x = (Math.random() - 0.5) * 10;
    mesh.position.y = (Math.random() - 0.5) * 10;
    mesh.position.z = (Math.random() - 0.5) * 5;
    
    // Random rotation
    mesh.rotation.x = Math.random() * Math.PI;
    mesh.rotation.y = Math.random() * Math.PI;
    
    // Random movement properties
    mesh.userData = {
      rotationSpeed: {
        x: (Math.random() - 0.5) * 0.01,
        y: (Math.random() - 0.5) * 0.01,
        z: (Math.random() - 0.5) * 0.01
      },
      movementSpeed: {
        x: (Math.random() - 0.5) * 0.005,
        y: (Math.random() - 0.5) * 0.005,
        z: (Math.random() - 0.5) * 0.005
      },
      movementRange: {
        x: { min: mesh.position.x - 1, max: mesh.position.x + 1 },
        y: { min: mesh.position.y - 1, max: mesh.position.y + 1 },
        z: { min: mesh.position.z - 0.5, max: mesh.position.z + 0.5 }
      }
    };
    
    objects.push(mesh);
    scene.add(mesh);
  }
}

function animate() {
  animationFrameId = requestAnimationFrame(animate);
  
  // Animate each object
  objects.forEach(obj => {
    // Rotation
    obj.rotation.x += obj.userData.rotationSpeed.x;
    obj.rotation.y += obj.userData.rotationSpeed.y;
    obj.rotation.z += obj.userData.rotationSpeed.z;
    
    // Movement
    obj.position.x += obj.userData.movementSpeed.x;
    obj.position.y += obj.userData.movementSpeed.y;
    obj.position.z += obj.userData.movementSpeed.z;
    
    // Reverse direction if reaching bounds
    const range = obj.userData.movementRange;
    
    if (obj.position.x < range.x.min || obj.position.x > range.x.max) {
      obj.userData.movementSpeed.x *= -1;
    }
    
    if (obj.position.y < range.y.min || obj.position.y > range.y.max) {
      obj.userData.movementSpeed.y *= -1;
    }
    
    if (obj.position.z < range.z.min || obj.position.z > range.z.max) {
      obj.userData.movementSpeed.z *= -1;
    }
  });
  
  renderer.render(scene, camera);
}

function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}
</script>