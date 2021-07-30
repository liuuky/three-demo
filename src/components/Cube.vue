<template>
    <div id="info">
        <a href="https://threejs.org" target="_blank" rel="noopener">three.js</a> webgl - interactive cubes
        <div class="box" v-if="show">
            车辆信息
        </div>
    </div>
</template>

<script>
import * as THREE from 'three';
// import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import Stats from 'three/examples/jsm/libs/stats.module'

let INTERSECTED;

// let theta = 0;
// const mouse = new THREE.Vector2();
const radius = 100;


export default {
    name: 'Cube',
    data() {
        return {
            scene: null, // 场景
            camera: null, // 摄像机
            renderer: null, // 渲染器
            geometry: null, // 几何体
            material: null, // 材质
            cube: null, // 正方体
            raycaster: null, // 射线
            mouse:  new THREE.Vector2(), // 鼠标位置
            controls: null, // 控制器
            stats: null,
            theta: 0,
            show: false,
        }
    },
    mounted() {
        this.init();
        this.animate();
    },
    methods: {
        init() {
            const container = document.createElement( 'div' );
			document.body.appendChild( container );
			this.camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 10000 );

            this.scene = new THREE.Scene();
			this.scene.background = new THREE.Color( 0xf0f0f0 );


            const light = new THREE.DirectionalLight( 0xffffff, 1 );
			light.position.set( 1, 1, 1 ).normalize();
			this.scene.add( light );

            const geometry = new THREE.BoxGeometry( 20, 20, 20 );

            for ( let i = 0; i < 2000; i ++ ) {

                const object = new THREE.Mesh( geometry, new THREE.MeshLambertMaterial( { color: Math.random() * 0xffffff } ) );

                object.position.x = Math.random() * 800 - 400;
                object.position.y = Math.random() * 800 - 400;
                object.position.z = Math.random() * 800 - 400;

                object.rotation.x = Math.random() * 2 * Math.PI;
                object.rotation.y = Math.random() * 2 * Math.PI;
                object.rotation.z = Math.random() * 2 * Math.PI;

                object.scale.x = Math.random() + 0.5;
                object.scale.y = Math.random() + 0.5;
                object.scale.z = Math.random() + 0.5;

                this.scene.add( object );

            }

            this.raycaster = new THREE.Raycaster();

            this.renderer = new THREE.WebGLRenderer();
            this.renderer.setPixelRatio( window.devicePixelRatio );
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            // document.body.appendChild(this.renderer.domElement);
            container.appendChild(this.renderer.domElement);

            this.stats = new Stats();
            console.log('stats', this.stats);
            container.appendChild( this.stats.dom );

			// document.addEventListener( 'mousemove', this.onMouseMove );
			document.addEventListener( 'click', this.onMouseMove );
			
            window.addEventListener( 'resize', this.onWindowResize );

            // this.animate();
        },
        onWindowResize() {
            this.camera.aspect = window.innerWidth / window.innerHeight;
            this.camera.updateProjectionMatrix();

            this.renderer.setSize( window.innerWidth, window.innerHeight );
        },
        onMouseMove( event ) {
			console.log( 'onMouseMove', event );
            this.mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
			this.mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;

            this.show = true;
        },
        animate() {
            requestAnimationFrame( this.animate );

            this.render();
            this.stats.update();
        },
        render() {
            this.theta += 0.1;

            // 设置摄像机旋转
            this.camera.position.x = radius * Math.sin( THREE.MathUtils.degToRad( this.theta ) );
            this.camera.position.y = radius * Math.sin( THREE.MathUtils.degToRad( this.theta ) );
            this.camera.position.z = radius * Math.cos( THREE.MathUtils.degToRad( this.theta ) );
            this.camera.lookAt( this.scene.position );

            this.camera.updateMatrixWorld();

            // this.mouse = new THREE.Vector2();
            // 通过摄像机和鼠标位置更新射线
            this.raycaster.setFromCamera(this.mouse, this.camera);
            
            // 计算物体和射线的焦点
            const intersects = this.raycaster.intersectObjects( this.scene.children );
            console.log('intersects', intersects);
            // let INTERSECTED;

            if ( intersects.length > 0 ) {

                if ( INTERSECTED != intersects[ 0 ].object ) {

                    if ( INTERSECTED ) INTERSECTED.material.emissive.setHex( INTERSECTED.currentHex );

                    INTERSECTED = intersects[ 0 ].object;
                    INTERSECTED.currentHex = INTERSECTED.material.emissive.getHex();
                    INTERSECTED.material.emissive.setHex( 0xff0000 );

                }

            } else {

                if ( INTERSECTED ) INTERSECTED.material.emissive.setHex( INTERSECTED.currentHex );

                INTERSECTED = null;

            }

            this.renderer.render(this.scene, this.camera);
        },
    }
}
</script>

<style>
/* body {
  width: 100vw;
  height: 100vh;
  margin: 0;
  overflow: hidden;
} */

body {
	margin: 0;
	background-color: #000;
	color: #fff;
	font-family: Monospace;
	font-size: 13px;
	line-height: 24px;
	overscroll-behavior: none;
}

a {
	color: #ff0;
	text-decoration: none;
}

a:hover {
	text-decoration: underline;
}

button {
	cursor: pointer;
	text-transform: uppercase;
}

#info {
	position: absolute;
	top: 0px;
	width: 100%;
	padding: 10px;
	box-sizing: border-box;
	text-align: center;
	-moz-user-select: none;
	-webkit-user-select: none;
	-ms-user-select: none;
	user-select: none;
	pointer-events: none;
	z-index: 1; /* TODO Solve this in HTML */
}

a, button, input, select {
	pointer-events: auto;
}

.dg.ac {
	-moz-user-select: none;
	-webkit-user-select: none;
	-ms-user-select: none;
	user-select: none;
	z-index: 2 !important; /* TODO Solve this in HTML */
}

#overlay {
	position: absolute;
	font-size: 16px;
	z-index: 2;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	background: rgba(0,0,0,0.7);
}

	#overlay button {
		background: transparent;
		border: 0;
		border: 1px solid rgb(255, 255, 255);
		border-radius: 4px;
		color: #ffffff;
		padding: 12px 18px;
		text-transform: uppercase;
		cursor: pointer;
	}

#notSupported {
	width: 50%;
	margin: auto;
	background-color: #f00;
	margin-top: 20px;
	padding: 10px;
}

.box {
    width: 300px;
    height: 300px;
    background: pink;
}

</style>