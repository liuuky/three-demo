<template>
  <div>
      <div id="info">
			<a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> car materials<br/>
			Ferrari 458 Italia model by <a href="https://sketchfab.com/models/57bf6cc56931426e87494f554df1dab6" target="_blank" rel="noopener">vicent091036</a>
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
const wheels = [];

export default {
    name: 'CarDemo',
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
            renderer.setAnimationLoop( this.render );
            renderer.outputEncoding = THREE.sRGBEncoding;
            renderer.toneMapping = THREE.ACESFilmicToneMapping;
            renderer.toneMappingExposure = 0.85;
            container.appendChild( renderer.domElement );

            window.addEventListener( 'resize', this.onWindowResize );

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

            grid = new THREE.GridHelper( 100, 40, 0x000000, 0x000000 );
            grid.material.opacity = 0.1;
            grid.material.depthWrite = false;
            grid.material.transparent = true;
            scene.add( grid );

            // materials

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

            // Car

            const shadow = new THREE.TextureLoader().load( 'ferrari/ferrari_ao.png' );

            const dracoLoader = new DRACOLoader();
            dracoLoader.setDecoderPath( 'ferrari/draco/gltf/' );

            const loader = new GLTFLoader();
            loader.setDRACOLoader( dracoLoader );

            loader.load( 'ferrari/ferrari.glb', function ( gltf ) {

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

                // shadow
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

        onWindowResize() {

            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();

            renderer.setSize( window.innerWidth, window.innerHeight );

        },
        render() {

            const time = - performance.now() / 1000;

            for ( let i = 0; i < wheels.length; i ++ ) {

                wheels[ i ].rotation.x = time * Math.PI;

            }

            grid.position.z = - ( time ) % 5;

            renderer.render( scene, camera );

            stats.update();

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
