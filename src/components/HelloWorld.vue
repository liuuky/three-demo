<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br>
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">vue-cli documentation</a>.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel" target="_blank" rel="noopener">babel</a></li>
      <li><a href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint" target="_blank" rel="noopener">eslint</a></li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li><a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a></li>
      <li><a href="https://forum.vuejs.org" target="_blank" rel="noopener">Forum</a></li>
      <li><a href="https://chat.vuejs.org" target="_blank" rel="noopener">Community Chat</a></li>
      <li><a href="https://twitter.com/vuejs" target="_blank" rel="noopener">Twitter</a></li>
      <li><a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a></li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li><a href="https://router.vuejs.org" target="_blank" rel="noopener">vue-router</a></li>
      <li><a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a></li>
      <li><a href="https://github.com/vuejs/vue-devtools#vue-devtools" target="_blank" rel="noopener">vue-devtools</a></li>
      <li><a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener">vue-loader</a></li>
      <li><a href="https://github.com/vuejs/awesome-vue" target="_blank" rel="noopener">awesome-vue</a></li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  methods: {
         inits() {
         
        this.scene = new THREE.Scene();
        this.scene.background = new THREE.Color(0xdddddd);

        this.camera = new THREE.PerspectiveCamera(40,window.innerWidth/window.innerHeight,1,5000);
        this.camera.rotation.y = 45/180*Math.PI;
        this.camera.position.x = 80;
        this.camera.position.y = 10;
        this.camera.position.z = 10;

        this.renderer = new THREE.WebGLRenderer({antialias:true});
        this.renderer.setSize(window.innerWidth,window.innerHeight);
        document.body.appendChild(this.renderer.domElement);


        let controls = new OrbitControls(this.camera,this.renderer.domElement);
        controls.addEventListener('change', this.renderer);

        let hlight = new THREE.AmbientLight (0x404040,100);
        this.scene.add(hlight);
        let directionalLight = new THREE.DirectionalLight(0xffffff,100);
        directionalLight.position.set(0,1,0);
        directionalLight.castShadow = true;
        this.scene.add(directionalLight);
        let light = new THREE.PointLight(0xc4c4c4,10);
        light.position.set(0,300,500);
        this.scene.add(light);
        let light2 = new THREE.PointLight(0xc4c4c4,10);
        light2.position.set(500,100,0);
        this.scene.add(light2);
        let light3 = new THREE.PointLight(0xc4c4c4,10);
        light3.position.set(0,100,-500);
        this.scene.add(light3);
        let light4 = new THREE.PointLight(0xc4c4c4,10);
        light4.position.set(-500,300,500);
        this.scene.add(light4);



        let loader = new GLTFLoader();
        loader.load('scene.gltf', (gltf) => {
            let car = gltf.scene.children[0];
            car.scale.set(0.5,0.5,0.5);
            this.scene.add(gltf.scene);
            this.animate();
        });
     },
     init() {
        let hlight, directionalLight,light,light2,light3,light4;

        this.scene = new THREE.Scene();
        this.scene.background = new THREE.Color(0xdddddd);
        this.camera = new THREE.PerspectiveCamera(40,window.innerWidth/window.innerHeight,1,5000);
        this.camera.rotation.y = 45/180*Math.PI;
        this.camera.position.x = 80;
        this.camera.position.y = 10;
        this.camera.position.z = 10;

        this.renderer = new THREE.WebGLRenderer({antialias:true});
        this.renderer.setSize(window.innerWidth,window.innerHeight);
        document.body.appendChild(this.renderer.domElement);

        let controls = new OrbitControls(this.camera,this.renderer.domElement);
        controls.addEventListener('change', this.renderer);
        hlight = new THREE.AmbientLight (0x404040,100);
        this.scene.add(hlight);
        directionalLight = new THREE.DirectionalLight(0xffffff,100);
        directionalLight.position.set(0,1,0);
        directionalLight.castShadow = true;
        this.scene.add(directionalLight);
        light = new THREE.PointLight(0xc4c4c4,10);
        light.position.set(0,300,500);
        this.scene.add(light);
        light2 = new THREE.PointLight(0xc4c4c4,10);
        light2.position.set(500,100,0);
        this.scene.add(light2);
        light3 = new THREE.PointLight(0xc4c4c4,10);
        light3.position.set(0,100,-500);
        this.scene.add(light3);
        light4 = new THREE.PointLight(0xc4c4c4,10);
        light4.position.set(-500,300,500);
        this.scene.add(light4);

        let loader = new GLTFLoader();
        loader.load('scene.gltf', (gltf) => {
            let car = gltf.scene.children[0];
            car.scale.set(0.5,0.5,0.5);
            this.scene.add(gltf.scene);
            this.animate();
        });
     },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
