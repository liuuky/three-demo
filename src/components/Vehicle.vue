<template>
  <div></div>
</template>

<script>
// 方式 1: 导入整个 three.js核心库
import * as THREE from "three";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";
// import { OBJLoader } from 'three/examples/jsm/loaders/OBJLoader';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
// import {  RGBELoader} from 'three/examples/jsm/loaders/RGBELoader'

export default {
  name: "Vehicle",
  data() {
    return {
      scene: null,
      camera: null,
      renderer: null,
      controls: null,
      mouse:  null,
    };
  },
  mounted() {
    this.init();
    console.log("window", window.innerHeight);
  },
  methods: {
    init() {
      this.scene = new THREE.Scene();
      this.scene.background = new THREE.Color(0xdddddd);

      this.camera = new THREE.PerspectiveCamera(
        40,
        window.innerWidth / window.innerHeight,
        1,
        5000
      );

      // this.camera.rotation.y = 45 / 180 * Math.PI;
      // this.camera.position.x = 800;
      // this.camera.position.y = 100;
      // this.camera.position.z = 1000;

      this.camera.position.set(700, 400, 800);

      this.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.renderer.setSize(window.innerWidth, window.innerHeight);
      document.body.appendChild(this.renderer.domElement);

      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      // controls.addEventListener('change', this.renderer);

      const hlight = new THREE.AmbientLight(0x404040, 100);
      this.scene.add(hlight);

      // const directionalLight = new THREE.DirectionalLight(0xffffff, 100);
      // directionalLight.position.set(0, 1, 0);
      // directionalLight.castShadow = true;
      // this.scene.add(directionalLight);

      const light = new THREE.PointLight(0xc4c4c4, 10);
      this.scene.add(light);

      const light2 = new THREE.PointLight(0xc4c4c4, 10);
      light2.position.set(500, 100, 0);
      this.scene.add(light2);

      const light3 = new THREE.PointLight(0xc4c4c4, 10);
      light3.position.set(0, 100, -500);
      this.scene.add(light3);

      const light4 = new THREE.PointLight(0xc4c4c4, 10);
      light4.position.set(-500, 300, 500);
      this.scene.add(light4);

      // 改变AxesHelper构造函数的参数，可以改变三维坐标轴的大小，
      // 参数设置坐标轴大小,150，编写程序的时候，可以根据相机参数调整为合适的值，如果太小可以无法显示出来
      const axesHelper = new THREE.AxesHelper( 550 );
      // 和网格模型Mesh一样，AxesHelper你也可以理解为一个模型对象，需要插入到场景中
      this.scene.add( axesHelper );

      const loader = new GLTFLoader();
      // const loader = new GLTFLoader().setPath();

      loader.load("scene.gltf", (gltf) => {
        const car = gltf.scene.children[0];
        console.log("car", car);
        console.log("gltf", gltf);
        car.scale.set(1.5, 1.5, 1.5);
        this.scene.add(gltf.scene);
        // 监听鼠标移动事件
        // window.addEventListener( 'mousemove', this.onMouseMove, false );
        // window.addEventListener( 'mousedown', this.onMouseMove, false );
        this.animate();
      });
    },
    animate() {
      // this.camera.position.x = time;
      // this.camera.position.y = time;
      // 设置摄像机旋转
      // const timer = Date.now() * 0.0001;
			// this.camera.position.z = Math.cos( timer ) * 800;
			// this.camera.position.x = Math.sin( timer ) * 800;
			// this.camera.lookAt( this.scene.position );

      // 光线投射Raycaster
      const raycaster = new THREE.Raycaster();
      this.mouse = new THREE.Vector2();
      // 通过摄像机和鼠标位置更新射线
      raycaster.setFromCamera( this.mouse, this.camera );
      // 计算物体和射线的焦点
      const intersects = raycaster.intersectObjects( this.scene.children, true );
      console.log(intersects);
      for ( let i = 0; i < intersects.length; i ++ ) {
        intersects[ i ].object.material.color.set( 0x000000 );
      }

      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },
    onMouseMove( event ) {
      console.log('mouse', this.mouse);
      // 将鼠标位置归一化为设备坐标。x 和 y 方向的取值范围是 (-1 to +1)
      this.mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
      this.mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;
    },
  },
};
</script>

<style>
body {
  width: 100vw;
  height: 100vh;
  margin: 0;
  overflow: hidden;
}
</style>
