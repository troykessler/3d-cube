<template>
  <div id="app"></div>
</template>

<script>
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import * as Cube from "cubejs";

const generate_scramble = () => {
  const moves = [
    ["U", "U'", "U2"],
    ["D", "D'", "D2"],
    ["R", "R'", "R2"],
    ["L", "L'", "L2"],
    ["F", "F'", "F2"],
    ["B", "B'", "B2"],
  ];

  let scramble = "";
  let lastMoveType = null;

  for (let i = 0; i < 20; i++) {
    let moveType = Math.floor(Math.random() * 6);
    moveType = moveType === lastMoveType ? (moveType + 1) % 6 : moveType;
    const move = Math.floor(Math.random() * 3);
    scramble += ` ${moves[moveType][move]}`;
    lastMoveType = moveType;
  }

  return scramble.trim();
};

var loader = new GLTFLoader();

let scene = new THREE.Scene();

var light = new THREE.AmbientLight(0xffffff, 2);
scene.add(light);

// Create a basic perspective camera
let camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
camera.position.z = 5;

// Create a renderer with Antialiasing
var renderer = new THREE.WebGLRenderer({
  antialias: true,
  precision: "mediump",
});

// Configure renderer clear color
renderer.setClearColor("#ffffff");

// Configure renderer size
renderer.setSize(window.innerWidth, window.innerHeight);

// Append Renderer to DOM
document.body.appendChild(renderer.domElement);

// ------------------------------------------------
// FUN STARTS HERE
// ------------------------------------------------

new OrbitControls(camera, renderer.domElement);

loader.load(
  "cube.gltf",
  (gltf) => {
    const cube = new Cube();
    const scramble = generate_scramble();
    cube.move(scramble);
    const scrambleString = cube.asString();
    console.log(scramble);
    console.log(scrambleString);

    scene.add(gltf.scene);
    console.log(gltf.scene);
    const cubeModel = gltf.scene.children[2];

    const tileOrder = [
      "U1",
      "U2",
      "U3",
      "U4",
      "U5",
      "U6",
      "U7",
      "U8",
      "U9",
      "R1",
      "R2",
      "R3",
      "R4",
      "R5",
      "R6",
      "R7",
      "R8",
      "R9",
      "F1",
      "F2",
      "F3",
      "F4",
      "F5",
      "F6",
      "F7",
      "F8",
      "F9",
      "D1",
      "D2",
      "D3",
      "D4",
      "D5",
      "D6",
      "D7",
      "D8",
      "D9",
      "L1",
      "L2",
      "L3",
      "L4",
      "L5",
      "L6",
      "L7",
      "L8",
      "L9",
      "B1",
      "B2",
      "B3",
      "B4",
      "B5",
      "B6",
      "B7",
      "B8",
      "B9",
    ];

    tileOrder.forEach((tile, index) => {
      const meshIndex = cubeModel.children.findIndex(
        (mesh) => mesh.material.name === tile
      );

      switch (scrambleString[index]) {
        case "U":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0.8,
            g: 0.8,
            b: 0.8,
            isColor: true,
          };
          break;
        case "R":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0.8,
            g: 0,
            b: 0,
            isColor: true,
          };
          break;
        case "F":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0,
            g: 0.8,
            b: 0,
            isColor: true,
          };
          break;
        case "D":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0.8,
            g: 0.8,
            b: 0,
            isColor: true,
          };
          break;
        case "L":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0.8,
            g: 0.3,
            b: 0.1,
            isColor: true,
          };
          break;
        case "B":
          gltf.scene.children[2].children[meshIndex].material.color = {
            r: 0,
            g: 0,
            b: 0.8,
            isColor: true,
          };
          break;
      }
    });
  },
  (xhr) => {
    // called while loading is progressing
    console.log(`${(xhr.loaded / xhr.total) * 100}% loaded`);
  },
  (error) => {
    // called when loading has errors
    console.error("An error happened", error);
  }
);

function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
}

animate();

export default {};
</script>

<style>
body {
  margin: 0;
}
canvas {
  display: block;
}
</style>
