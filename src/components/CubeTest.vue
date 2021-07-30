<template>
  <div>cube</div>
</template>

<script>
import * as THREE from 'three';
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import Stats from 'three/examples/jsm/libs/stats.module'

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
            mouse:  null, // 鼠标位置
            controls: null, // 控制器
            stats: null,
        }
    },
    mounted() {
        this.init();
        // this.animate();
    },
    methods: {
        init() {
            this.scene = new THREE.Scene();
			this.scene.background = new THREE.Color( 0xf0f0f0 );

            this.camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 1, 1000);

            this.renderer = new THREE.WebGLRenderer();
            this.renderer.setPixelRatio( window.devicePixelRatio );
            this.renderer.setSize(window.innerWidth, window.innerHeight);
            document.body.appendChild(this.renderer.domElement);
            this.stats = new Stats();
            console.log('stats', this.stats);
            document.body.appendChild( this.stats.dom );

            this.controls = new OrbitControls(this.camera, this.renderer.domElement);


            // 灯光设置
            {
                // const color = 0xFFFFFF;
                // const intensity = 1;
                // const light = new THREE.DirectionalLight(color, intensity);
                // light.position.set(-1, 2, 4);
                // this.scene.add(light);
                const light = new THREE.DirectionalLight( 0xffffff, 1 );
				light.position.set( 1, 1, 1 ).normalize();
				this.scene.add( light );
            }

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

            // this.geometry = new THREE.BoxGeometry(1, 1, 1);
            // this.material = new THREE.MeshBasicMaterial( { color: 0x44aa88 });
            this.material = new THREE.MeshPhongMaterial( { color: 0x44aa88 }); // 受灯光影响材质，无灯光不显示物体
            // this.cube = new THREE.Mesh(this.geometry, this.material);

            // this.scene.add(this.cube);

            this.camera.position.z = 5;

			// document.addEventListener( 'mousemove', this.onMouseMove );
			document.addEventListener( 'mouseover', this.onMouseMove );
            // window.addEventListener( 'mousedown', this.onMouseMove, false );
            this.animate();
        },
        animate() {
            requestAnimationFrame( this.animate );

            // this.cube.rotation.x += 0.01;
            // this.cube.rotation.y += 0.01;
            let theta = 0;
            theta += 0.1;
			const radius = 100;

            this.camera.position.x = radius * Math.sin( THREE.MathUtils.degToRad( theta ) );
            this.camera.position.y = radius * Math.sin( THREE.MathUtils.degToRad( theta ) );
            this.camera.position.z = radius * Math.cos( THREE.MathUtils.degToRad( theta ) );
            this.camera.lookAt( this.scene.position );

            // 设置摄像机旋转
            // const timer = Date.now() * 0.0001;
			// this.camera.position.z = Math.cos( timer ) * 800;
			// this.camera.position.x = Math.sin( timer ) * 800;
			// this.camera.lookAt( this.scene.position );

            this.camera.updateMatrixWorld();

            this.raycaster = new THREE.Raycaster();
            this.mouse = new THREE.Vector2();

            this.raycaster.setFromCamera(this.mouse, this.camera);

            const intersects = this.raycaster.intersectObjects( this.scene.children );
            console.log('intersects', intersects);
            let INTERSECTED;

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

            // const intersection = this.raycaster.intersectObjects( this.cube );
            // if ( intersection.length > 0 ) {
            //     const instanceId = intersection[0].instanceId;
            //     this.cube.setColor(instanceId, this.color.setHex( 0xff0000 ));
            //     this.cube.instanceColor.needsUpdate = true;
            // }
            // const intersects = this.raycaster.intersectObjects( this.scene.children );
            // console.log(intersects);
            // for ( let i = 0; i < intersects.length; i ++ ) {
            //     intersects[ i ].object.material.color.set( 0xff0000 );
            // }

            // setTimeout(() => {
            //     this.resetSelect();
            // }, 2000);
            // this.hoverSelect();
            this.stats.update();

            this.renderer.render(this.scene, this.camera);
        },
        resetSelect() {
            for (let i = 0; i < this.scene.children.length; i++) {
                if (this.scene.children[i].material) {
                    this.scene.children[i].material.color.set( 0x44aa88 );
                    this.scene.children[i].material.opacity = 1.0
                }                
            }
        },
        hoverSelect() {
            console.log('mouse', this.mouse);
            const intersects = this.raycaster.intersectObjects( this.scene.children );
            console.log(intersects);
            for ( let i = 0; i < intersects.length; i ++ ) {
                intersects[ i ].object.material.color.set( 0xff0000 );
                intersects[ i ].object.material.transparent = true;
                intersects[ i ].object.material.opacity = 0.5;
            }
        },
        onMouseMove( event ) {
            event.preventDefault();
            this.mouse.x = ( event.clientX / window.innerWidth ) * 2 - 1;
            this.mouse.y = - ( event.clientY / window.innerHeight ) * 2 + 1;
            this.hoverSelect();

        }
    }
}
</script>

<style>

</style>