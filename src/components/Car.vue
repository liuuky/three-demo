<template>
  <div>
      <div id="info">
			<!-- <a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> car materials<br/>
			Ferrari 458 Italia model by <a href="https://sketchfab.com/models/57bf6cc56931426e87494f554df1dab6" target="_blank" rel="noopener">vicent091036</a> -->
			<br><br>
			<span class="colorPicker"><input id="body-color" type="color" value="#ff0000" /><br/>Body</span>
			<span class="colorPicker"><input id="details-color" type="color" value="#ffffff" /><br/>Details</span>
			<span class="colorPicker"><input id="glass-color" type="color" value="#ffffff" /><br/>Glass</span>
		</div>

		<div id="container"></div>
  </div>
</template>

<script>
import * as THREE from 'three';
import Stats from 'three/examples/jsm/libs/stats.module'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';
import { RoomEnvironment } from 'three/examples/jsm/environments/RoomEnvironment';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { DRACOLoader } from 'three/examples/jsm/loaders/DRACOLoader';

let camera, scene, renderer;
let stats;

let grid;
let controls;
let lightHelper;
const wheels = [];

const poiPosArray=[
    {x:-1.47,y:0.87,z:-0.36,frame:1},
    {x:-1.46,y:0.49,z:-0.69,frame:2},
    {x:1.5,y:.7,z:0,frame:8},
    {x:0.33,y:1.79,z:0,frame:3},
    
    {x:0.73,y:1.38,z:-0.8,frame:5},
    {x:-.1,y:1.17,z:0.88,frame:6},
    {x:-1.16,y:0.16,z:0.89,frame:7}
],poiObjects=[];

export default {
    name: 'Car',
    data() {
        return {

        }
    },
    mounted() {
        this.init();
    },
    methods: {
        init() {

            const container = document.getElementById( 'container' );

            renderer = new THREE.WebGLRenderer( { antialias: true } );
            renderer.setPixelRatio( window.devicePixelRatio );
            renderer.setSize( window.innerWidth, window.innerHeight );
            /* 
              callback — 每个可用帧都会调用的函数。 如果传入‘null’,所有正在进行的动画都会停止。
              可用来代替requestAnimationFrame的内置函数. 对于WebXR项目，必须使用此函数。
            */
            renderer.setAnimationLoop( this.render );
            renderer.outpustEncoding = THREE.sRGBEncoding;
            // 默认是NoToneMapping。查看Renderer constants以获取其它备选项
            renderer.toneMapping = THREE.ACESFilmicToneMapping;  
            // 色调映射的曝光级别。默认是1
            renderer.toneMappingExposure = 0.85;  
            container.appendChild( renderer.domElement );

            window.addEventListener( 'resize', this.onWindowResize );

            // 显示帧数的性能插件
            stats = new Stats();
            container.appendChild( stats.dom );

            //

            camera = new THREE.PerspectiveCamera( 40, window.innerWidth / window.innerHeight, 0.1, 100 );
            camera.position.set( 4.25, 1.4, - 4.5 );

            controls = new OrbitControls( camera, container );
            controls.target.set( 0, 0.5, 0 );
            controls.update();

            const pmremGenerator = new THREE.PMREMGenerator( renderer );

            scene = new THREE.Scene();
            scene.background = new THREE.Color( 0xeeeeee );
            scene.environment = pmremGenerator.fromScene( new RoomEnvironment() ).texture;
            scene.fog = new THREE.Fog( 0xeeeeee, 10, 50 );

            // 网格辅助线
            grid = new THREE.GridHelper( 100, 40, 0x000000, 0x000000 );
            grid.material.opacity = 0.1;
            grid.material.depthWrite = false;
            grid.material.transparent = true;
            scene.add( grid );
            
            /* 坐标辅助线
                改变AxesHelper构造函数的参数，可以改变三维坐标轴的大小，
                参数设置坐标轴大小,150，编写程序的时候，可以根据相机参数调整为合适的值，如果太小可以无法显示出来
            */
            const axesHelper = new THREE.AxesHelper( 10 );
            // 和网格模型Mesh一样，AxesHelper你也可以理解为一个模型对象，需要插入到场景中
            scene.add( axesHelper );

            // materials---------------------------------------------------------------

            const bodyMaterial = new THREE.MeshPhysicalMaterial( {
                color: 0xff0000, metalness: 0.6, roughness: 0.4, clearcoat: 0.05, clearcoatRoughness: 0.05
            } );

            const detailsMaterial = new THREE.MeshStandardMaterial( {
                color: 0xffffff, metalness: 1.0, roughness: 0.5
            } );

            const glassMaterial = new THREE.MeshPhysicalMaterial( {
                color: 0xffffff, metalness: 0, roughness: 0.1, transmission: 0.9, transparent: true
            } );

            const bodyColorInput = document.getElementById( 'body-color' );
            bodyColorInput.addEventListener( 'input', function () {

                bodyMaterial.color.set( this.value );

            } );

            const detailsColorInput = document.getElementById( 'details-color' );
            detailsColorInput.addEventListener( 'input', function () {

                detailsMaterial.color.set( this.value );

            } );

            const glassColorInput = document.getElementById( 'glass-color' );
            glassColorInput.addEventListener( 'input', function () {

                glassMaterial.color.set( this.value );

            } );

            // light---------------------------------------------------------------
            const light = new THREE.DirectionalLight( 0xffffff, 1 );
            light.position.set( 0, 30, 0 ).normalize();
            scene.add(light);

            // 灯光辅助线
            lightHelper = new THREE.SpotLightHelper( light );
			scene.add( lightHelper );

            // Car---------------------------------------------------------------

            const shadow = new THREE.TextureLoader().load( 'ferrari/ferrari_ao.png' );

            const dracoLoader = new DRACOLoader();
            dracoLoader.setDecoderPath( 'ferrari/draco/gltf/' );

            const loader = new GLTFLoader();
            loader.setDRACOLoader( dracoLoader );

            loader.load( 'ferrari/ferrari.glb', ( gltf ) => {
                // 设置精灵贴图
                // const poiPosArray=[
                //     {x:-1.47,y:0.87,z:-0.36,frame:1},
                //     {x:-1.46,y:0.49,z:-0.69,frame:2},
                //     {x:1.5,y:.7,z:0,frame:8},
                //     {x:0.33,y:1.79,z:0,frame:3},
                    
                //     {x:0.73,y:1.38,z:-0.8,frame:5},
                //     {x:-.1,y:1.17,z:0.88,frame:6},
                //     {x:-1.16,y:0.16,z:0.89,frame:7}
                // ],poiObjects=[];

                const pointTexture = new THREE.TextureLoader().load("point.png");

                const group = new THREE.Group();
                const materialC = new THREE.SpriteMaterial( { map: pointTexture, color: 0xffffff, fog: false } );
                for ( let a = 0; a < poiPosArray.length; a ++ ) {
                    const x = poiPosArray[a].x;
                    const y = poiPosArray[a].y-.5;
                    const z = poiPosArray[a].z;

                    const sprite = new THREE.Sprite( materialC );
                    sprite.scale.set( .15, .15, 1 );
                    sprite.position.set( x, y, z );
                    sprite.idstr="popup_"+poiPosArray[a].frame;
                    group.add( sprite );

                    poiObjects.push(sprite);
                }
                scene.add( group );
                
                // 通过射线与精灵图绑定点击事件
                document.body.addEventListener("click", this.onMouseMove);

                const carModel = gltf.scene.children[ 0 ];

                carModel.getObjectByName( 'body' ).material = bodyMaterial;

                carModel.getObjectByName( 'rim_fl' ).material = detailsMaterial;
                carModel.getObjectByName( 'rim_fr' ).material = detailsMaterial;
                carModel.getObjectByName( 'rim_rr' ).material = detailsMaterial;
                carModel.getObjectByName( 'rim_rl' ).material = detailsMaterial;
                carModel.getObjectByName( 'trim' ).material = detailsMaterial;

                carModel.getObjectByName( 'glass' ).material = glassMaterial;

                wheels.push(
                    carModel.getObjectByName( 'wheel_fl' ),
                    carModel.getObjectByName( 'wheel_fr' ),
                    carModel.getObjectByName( 'wheel_rl' ),
                    carModel.getObjectByName( 'wheel_rr' )
                );

                // shadow---------------------------------------------------------------
                const mesh = new THREE.Mesh(
                    new THREE.PlaneGeometry( 0.655 * 4, 1.3 * 4 ),
                    new THREE.MeshBasicMaterial( {
                        map: shadow, blending: THREE.MultiplyBlending, toneMapped: false, transparent: true
                    } )
                );
                mesh.rotation.x = - Math.PI / 2;
                mesh.renderOrder = 2;
                carModel.add( mesh );

                scene.add( carModel );

            } );

        },
        // 通过射线与坐标位置获取对应点击位置
        onMouseMove( event ) {
            // 将鼠标位置归一化为设备坐标。x 和 y 方向的取值范围是 (-1 to +1)
            event.preventDefault();
            var raycaster = new THREE.Raycaster();
            var mouse = new THREE.Vector2();
            mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
            mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

            raycaster.setFromCamera( mouse, camera );

            var intersects = raycaster.intersectObjects( poiObjects );
            if(intersects.length>0){
                var popIndex=parseInt(intersects[ 0 ].object.idstr.substr(6,1));
                console.log(popIndex);
                alert('111');
            }
        },

        onWindowResize() {

            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();

            renderer.setSize( window.innerWidth, window.innerHeight );

        },
        render() {

            const time = - performance.now() / 1000;

            for ( let i = 0; i < wheels.length; i ++ ) {
                // 控制汽车车轮转动
                wheels[ i ].rotation.x = time * Math.PI;

            }

            // 控制网格布局移动
            grid.position.z = - ( time ) % 5;

            renderer.render( scene, camera );

            stats.update();
			
            lightHelper.update();

        }
    }

}
</script>

<style>
    body {
        color: #444;
        background: #eee;
    }
    a {
        color: #08f;
    }
    .colorPicker {
        display: inline-block;
        margin: 0 10px
    }
</style>
<style>

</style>
