<template>
  <div class="home">
    <div class="my-pages" ref="pages">
      <!-- <div class="my-page">
        <h2 class="my-title">摸鱼老萌新的博客</h2>
        <p>轻松、好玩、有趣掌握前沿前端技术</p>
        <div>
          <a href="https://github.com/aiai0603">
            <div class="my-button">我的GIT ✉️</div>
          </a>
        </div>
      </div> -->
      <div class="my-page">
        <h2 class="my-title">前端/IT技术分享</h2>
        <p>从前端入门、JS刷题到最新潮技术</p>
        <div style="display: flex; flex-flow: row">
          <a href="/articles/basic/index">
            <div class="my-button">知识 💡</div>
          </a>
          <a href="/leetcode/LEET_CODE题解/47. 全排列 II" style="margin-left: 10px">
            <div class="my-button">刷题 📄</div>
          </a>
        </div>
      </div>
      <div class="my-page">
        <h2 class="my-title">前端开源项目分享</h2>
        <p>共享不同技术的前端项目和实用工具</p>
        <div>
          <a href="/articles/intent/index">
            <div class="my-button">项目列表 📦</div>
          </a>
        </div>
      </div>
    </div>
    <div class="canvas-container" ref="screenDom"></div>
  </div>
</template>
  
<script setup>
import * as THREE from 'three';
import { ref, onMounted, onUnmounted } from 'vue';
import { gsap } from 'gsap';
let screenDom = ref(null);
let pages = ref(null);

let auto = ref(null);
let mouse = null;
let page = 0;
let group = null;
let timeline = null;
let timeline2 = null;
let renderer = null;
let raycaster = null;
let scene = null;
let camera = null;

const lon2xyz = (R, longitude, latitude) => {
  let lon = (longitude * Math.PI) / 180; // 转弧度值
  const lat = (latitude * Math.PI) / 180; // 转弧度值
  lon = -lon; // js坐标系z坐标轴对应经度-90度，而不是90度

  // 经纬度坐标转球面坐标计算公式
  const x = R * Math.cos(lat) * Math.cos(lon);
  const y = R * Math.sin(lat);
  const z = R * Math.cos(lat) * Math.sin(lon);
  // 返回球面坐标
  return new THREE.Vector3(x, y, z);
};

const handleOver = (e) => {
  if (!auto.value) {
    let x = (e.clientX / window.innerWidth) * 2 - 1;
    let y = -(e.clientY / window.innerHeight) * 2 + 1;
    if (timeline2.isActive()) {
      timeline2.clear();
    }
    group.rotation.order = 'YXZ';
    timeline2.to(group.rotation, {
      duration: 0.5,
      y: (Math.PI / 4) * x,
      x: -(Math.PI / 4) * y,
      ease: 'easInOut',
    });
  }
};

const handleWheel = (e) => {
  if (e.wheelDelta < 0) {
    page++;
    if (page > 2) {
      page = 2;
    }
  }
  if (e.wheelDelta > 0) {
    page--;
    if (page < 0) {
      page = 0;
    }
  }

  if (!timeline.isActive()) {
    timeline.to(camera.position, {
      duration: 1,
      y: -(page - 1) * 30 + 10,
      ease: 'easeInOut',
    });
    gsap.to(pages.value, {
      duration: 1,
      y: -page * window.innerHeight,
      ease: 'easeInOut',
    });
  }
};

const handleClick = (event) => {
  //通过鼠标点击的地位计算出raycaster所须要的点的地位，以屏幕核心为原点，值的范畴为-1到1.
  mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

  // 通过鼠标点的地位和以后相机的矩阵计算出raycaster
  raycaster.setFromCamera(mouse, camera);

  // 获取raycaster直线和所有模型相交的数组汇合
  var intersects = raycaster.intersectObjects(scene.children);
  if (intersects.length !== 0) {
    for (var i = 0; i < intersects.length; i++) {
      if (intersects[i].object.name == 'earth') {
        auto.value = !auto.value;
      }
    }
  }
};

const handleResize = () => {
  // 更新摄像头
  camera.aspect = window.innerWidth / window.innerHeight;
  //   更新摄像机的投影矩阵
  camera.updateProjectionMatrix();

  //   更新渲染器
  renderer.setSize(window.innerWidth, window.innerHeight);
  //   设置渲染器的像素比
  renderer.setPixelRatio(window.devicePixelRatio);
};

let GLTFLoader = null;

let DRACOLoader = null;
onUnmounted(() => {
  window.removeEventListener('click', handleClick, false);
  window.removeEventListener('mousemove', handleOver);
  window.removeEventListener('mousewheel', handleWheel, { passive: false });
  window.removeEventListener('resize', handleResize);
});

onMounted(() => {
  import('three/examples/jsm/loaders/GLTFLoader').then((module) => {
    GLTFLoader = module.GLTFLoader;
    import('three/examples/jsm/loaders/DRACOLoader').then((module) => {
      DRACOLoader = module.DRACOLoader;

      // 创建场景
      scene = new THREE.Scene();
      // 创建相机
      camera = new THREE.PerspectiveCamera(
        45,
        screenDom.value.clientWidth / screenDom.value.clientHeight,
        0.1,
        1000
      );
      camera.position.set(0, 40, 300);

      renderer = new THREE.WebGLRenderer({ antialias: true });
      renderer.setSize(
        screenDom.value.clientWidth,
        screenDom.value.clientHeight
      );
      screenDom.value.appendChild(renderer.domElement);

      let url = './assets/25s.jpg';
      let envTexture = new THREE.TextureLoader().load(url);
      envTexture.mapping = THREE.EquirectangularRefractionMapping;
      scene.background = envTexture;
      scene.environment = envTexture;

      group = new THREE.Group();

      let earthGeometry = new THREE.SphereGeometry(50, 32, 32);
      let earthTexture = new THREE.TextureLoader().load('./images/map.jpg');

      let earthMaterial = new THREE.MeshBasicMaterial({
        map: earthTexture,
      });
      let earth = new THREE.Mesh(earthGeometry, earthMaterial);
      earth.name = 'earth';
      group.add(earth);
      //scene.add(earth);
      // 发光地球
      let lightTexture = new THREE.TextureLoader().load('./images/earth.jpg');
      let lightEarthGeometry = new THREE.SphereGeometry(53, 32, 32);
      let lightEarthMaterial = new THREE.MeshBasicMaterial({
        map: lightTexture,
        alphaMap: lightTexture,
        blending: THREE.AdditiveBlending,
        transparent: true,
      });
      let lightEarth = new THREE.Mesh(lightEarthGeometry, lightEarthMaterial);

      group.add(lightEarth);

      // 添加地球内外发光精灵
      let spriteTexture = new THREE.TextureLoader().load('./images/glow.png');
      let spriteMaterial = new THREE.SpriteMaterial({
        map: spriteTexture,
        color: 0x4d76cf,
        transparent: true,
        depthWrite: false,
        depthTest: false,
        blending: THREE.AdditiveBlending,
      });
      let sprite = new THREE.Sprite(spriteMaterial);
      sprite.scale.set(155, 155, 0);

      group.add(sprite);

      // 内发光
      let spriteTexture1 = new THREE.TextureLoader().load(
        './images/innerGlow.png'
      );
      let spriteMaterial1 = new THREE.SpriteMaterial({
        map: spriteTexture1,
        color: 0x4d76cf,
        transparent: true,
        depthWrite: false,
        depthTest: false,
        blending: THREE.AdditiveBlending,
      });
      let sprite1 = new THREE.Sprite(spriteMaterial1);
      sprite1.scale.set(128, 128, 0);

      group.add(sprite1);

      scene.add(group);
      let scale = new THREE.Vector3(1, 1, 1);

      for (let i = 0; i < 30; i++) {
        // 实现光柱
        let lightPillarTexture = new THREE.TextureLoader().load(
          './images/light_column.png'
        );
        let lightPillarGeometry = new THREE.PlaneGeometry(3, 20);
        let lightPillarMaterial = new THREE.MeshBasicMaterial({
          color: 0xffffff,
          map: lightPillarTexture,
          alphaMap: lightPillarTexture,
          transparent: true,
          blending: THREE.AdditiveBlending,
          side: THREE.DoubleSide,
          depthWrite: false,
        });
        let lightPillar = new THREE.Mesh(
          lightPillarGeometry,
          lightPillarMaterial
        );
        lightPillar.add(lightPillar.clone().rotateY(Math.PI / 2));

        // 创建波纹扩散效果
        let circlePlane = new THREE.PlaneGeometry(6, 6);
        let circleTexture = new THREE.TextureLoader().load(
          './images/label.png'
        );
        let circleMaterial = new THREE.MeshBasicMaterial({
          color: 0xffffff,
          map: circleTexture,
          transparent: true,
          blending: THREE.AdditiveBlending,
          depthWrite: false,
          side: THREE.DoubleSide,
        });
        let circleMesh = new THREE.Mesh(circlePlane, circleMaterial);
        circleMesh.rotation.x = -Math.PI / 2;
        circleMesh.position.set(0, -7, 0);

        lightPillar.add(circleMesh);

        gsap.to(circleMesh.scale, {
          duration: 1 + Math.random() * 0.5,
          x: 2,
          y: 2,
          z: 2,
          repeat: -1,
          delay: Math.random() * 0.5,
          yoyo: true,
          ease: 'power2.inOut',
        });

        // 设置光柱的位置
        // lightPillar.position.set(0, 50, 0);
        let lat = Math.random() * 180 - 90;
        let lon = Math.random() * 360 - 180;
        let position = lon2xyz(60, lon, lat);
        lightPillar.position.set(position.x, position.y, position.z);

        lightPillar.quaternion.setFromUnitVectors(
          new THREE.Vector3(0, 1, 0),
          position.clone().normalize()
        );
        group.add(lightPillar);
        group.position.x = 75;
        scene.add(group);
      }

      // 绕地球运行的月球
      let moonTexture = new THREE.TextureLoader().load('./images/moon.jpg');
      let moonMaterial = new THREE.MeshStandardMaterial({
        map: moonTexture,
        emissive: 0xffffff,
        emissiveMap: moonTexture,
      });
      let moonGeometry = new THREE.SphereGeometry(5, 32, 32);
      let moon = new THREE.Mesh(moonGeometry, moonMaterial);
      moon.position.set(225, 0, 0);
      scene.add(moon);

      // 创建月球环
      let moonRingTexture = new THREE.TextureLoader().load(
        './images/moon_ring.png'
      );
      let moonRingMaterial = new THREE.MeshBasicMaterial({
        map: moonRingTexture,
        transparent: true,
        blending: THREE.AdditiveBlending,
        side: THREE.DoubleSide,
        depthWrite: false,
        opacity: 0.5,
      });
      let moonRingGeometry = new THREE.RingGeometry(145, 155, 64);
      let moonRing = new THREE.Mesh(moonRingGeometry, moonRingMaterial);
      moonRing.rotation.x = -Math.PI / 2;
      moonRing.position.x = 75;
      scene.add(moonRing);

      let time = {
        value: 0,
      };
      gsap.to(time, {
        value: 1,
        duration: 10,
        repeat: -1,
        ease: 'linear',
        onUpdate: () => {
          if (auto.value) {
            group.rotation.y = time.value * Math.PI * 2;
          }

          moon.position.x = 150 * Math.cos(time.value * Math.PI * 2) + 75;
          moon.position.z = 150 * Math.sin(time.value * Math.PI * 2);
          moon.rotation.y = time.value * Math.PI * 8;
        },
      });

      raycaster = new THREE.Raycaster();
      mouse = new THREE.Vector2();
      window.addEventListener('click', handleClick, false);

      // 添加机器
      // 设置解压缩的加载器
      let dracoLoader = new DRACOLoader();
      dracoLoader.setDecoderPath('./draco/gltf/');
      dracoLoader.setDecoderConfig({ type: 'js' });
      let gltfLoader = new GLTFLoader();
      gltfLoader.setDRACOLoader(dracoLoader);

      gltfLoader.load('./model/moon.glb', (gltf) => {
        let moon = gltf.scene.children[0];

        for (let y = 0; y < 10; y++) {
          let moonInstanced = new THREE.InstancedMesh(
            moon.geometry,
            moon.material,
            50
          );
          for (let i = 0; i < 50; i++) {
            let matrix = new THREE.Matrix4();
            let size = Math.random() * 10 - 8;
            matrix.makeScale(size, size, size);
            matrix.makeTranslation(
              Math.random() * 1000 - 500,
              Math.random() * 1000 - 500,
              Math.random() * 1000 - 500
            );
            // matrix.makeRotationY(Math.random() * Math.PI * 2);

            moonInstanced.setMatrixAt(i, matrix);
          }

          scene.add(moonInstanced);
          // moonInstanced.position.set(0, -2000, 0);
          // gsap.to(moonInstanced.rotation, {
          //   duration: 100,
          //   x: -2 * Math.PI,
          //   repeat: -1,
          //   ease: "linear",
          // });

          gsap.to(moonInstanced.position, {
            duration: Math.random() * 10 + 2,
            z: -1000,
            repeat: -1,
            ease: 'linear',
          });
        }

        timeline = gsap.timeline();
        timeline2 = gsap.timeline();

        window.addEventListener('mousemove', handleOver);

        window.addEventListener('mousewheel', handleWheel, { passive: false });
      });

      // 添加直线光
      let light1 = new THREE.DirectionalLight(0xffffff, 0.3);
      light1.position.set(0, 10, 10);
      let light2 = new THREE.DirectionalLight(0xffffff, 0.3);
      light1.position.set(0, 10, -10);
      let light3 = new THREE.DirectionalLight(0xffffff, 0.8);
      light1.position.set(10, 10, 10);
      scene.add(light1, light2, light3);

      function render() {
        requestAnimationFrame(render);
        renderer.render(scene, camera);
      }
      render();

      // 监听画面变化，更新渲染画面
      window.addEventListener('resize', handleResize);
    });
  });
});
</script>
  
<style  scoped>
* {
  margin: 0;
  padding: 0;
}
.canvas-container {
  width: 100vw;
  height: 100vh;
}
.home {
  width: 100vw;
  height: 100vh;
  transform-origin: 0 0;
}

.canvas-container {
  width: 100%;
  height: 100%;
}
.menu {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-right: 50px;
}
.menuItem {
  padding: 0 15px;
  text-decoration: none;
  color: #fff;
  font-weight: 900;
  font-size: 15px;
}
.loading {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-image: url(./assets/loading.jpg);
  background-size: cover;
  filter: blur(50px);
  z-index: 100;
}
.progress {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 101;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 20px;
  color: #fff;
}
.progress > img {
  padding: 0 15px;
}

/* .title {
    width: 380px;
    height: 40px;
    position: fixed;
    right: 100px;
    top: 50px;
    background-color: rgba(0, 0, 0, 0.5);
    line-height: 40px;
    text-align: center;
    color: #fff;
    border-radius: 5px;
    z-index: 110;
  } */
.my-pages {
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
}
.my-pages .my-page {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  color: #fff;
  padding: 15%;
  box-sizing: border-box;
}

.my-pages .my-page .my-title {
  font-size: 50px;
  font-weight: 900;
  margin-bottom: 20px;
}
.my-pages .my-page .p {
  font-size: 36px;
}

.my-pages .my-page .my-button {
  font-size: 20px;
  border: 2px #fff solid;
  padding: 10px 20px;
  border-radius: 30px;
  margin-top: 10px;
  letter-spacing: 1px;
}
</style>