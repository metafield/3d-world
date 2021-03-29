<script lang="ts">
  import { OrbitControls } from './utils/orbit'
  import { onMount } from 'svelte'
  import * as THREE from 'three'
  export let width: number
  export let height: number

  let threeContainer

  const threejs = new THREE.WebGLRenderer()
  threejs.shadowMap.enabled = true
  threejs.shadowMap.type = THREE.PCFSoftShadowMap
  threejs.setPixelRatio(window.devicePixelRatio)
  threejs.setSize(width, height)

  const fov = 60
  const aspect = 1024 / 768
  const near = 1.0
  const far = 1000.0
  const camera = new THREE.PerspectiveCamera(fov, aspect, near, far)
  camera.position.set(50, 20, 0)

  const scene = new THREE.Scene()

  let light = new THREE.DirectionalLight(0xffffff)
  light.position.set(100, 100, 100)
  light.target.position.set(0, 0, 0)
  light.castShadow = true
  light.shadow.bias = -0.01
  light.shadow.mapSize.width = 2048
  light.shadow.mapSize.height = 2048
  light.shadow.camera.near = 1.0
  light.shadow.camera.far = 500
  light.shadow.camera.left = 200
  light.shadow.camera.right = -200
  light.shadow.camera.top = 200
  light.shadow.camera.bottom = -200
  scene.add(light)

  let ambientLight = new THREE.AmbientLight(0x404040)
  scene.add(ambientLight)

  const controls = new OrbitControls(camera, threejs.domElement)
  controls.target.set(0, 20, 0)
  controls.update

  const loader = new THREE.CubeTextureLoader()
  const texture = loader.load([
    '/assets/posx.jpg',
    '/assets/negx.jpg',
    '/assets/posy.jpg',
    '/assets/negy.jpg',
    '/assets/posz.jpg',
    '/assets/negz.jpg',
  ])

  scene.background = texture

  const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(100, 100, 1, 1),
    new THREE.MeshStandardMaterial({
      color: 0xffffff,
    })
  )
  plane.castShadow = false
  plane.receiveShadow = true
  plane.rotation.x = -Math.PI / 2
  scene.add(plane)

  const box = new THREE.Mesh(
    new THREE.BoxGeometry(2, 2, 2),
    new THREE.MeshStandardMaterial({
      color: 0x1020ff,
    })
  )
  box.position.set(0, 1, 0)
  scene.add(box)

  RAF()

  function RAF() {
    requestAnimationFrame(() => {
      threejs.render(scene, camera)
      RAF()
    })
  }

  onMount(() => {
    threeContainer.appendChild(threejs.domElement)
  })
</script>

<div bind:this={threeContainer} />
