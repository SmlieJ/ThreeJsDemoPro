<template>
  <div>
    <div id="container"></div>
  </div>
</template>
 
<script>
//这里是基础的说明，包括几何体的位移、旋转、缩放等，还有动画特效的时间
import * as Three from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
// 几何、场景、控制器必须放到外面
let scene;
let mesh;
let controls;
//时间触发器
// let clock = new Three.Clock();
let time;
let daltaTime;
export default {
  name: "ThreeTest",
  data() {
    return {
      camera: null,
      //   scene: null,
      renderer: null,
      //   mesh: null,
      clock: null,
    };
  },
  methods: {
    init: function () {
      this.clock = new Three.Clock();
      // console.log(123)
      let container = document.getElementById("container");
      //透视投影机 相机
      //第一个是相机平截头体垂直视野   摄像机视锥体垂直视野角度
      //第二个是相机平截头体纵横比   摄像机视锥体长宽比
      //第三个是平面附近的相机平截头体   摄像机视锥体近端面
      //相机平截头体远平面   摄像机视锥体远端面
      this.camera = new Three.PerspectiveCamera(
        80,
        container.clientWidth / container.clientHeight,
        0.02,
        10
      );
      //设置相机位置
      this.camera.position.z = 3;
      //场景
      this.scene = new Three.Scene();
      //箱子几何
      let geometry = new Three.BoxGeometry(0.2, 0.2, 0.2);
      //材料
      let material = new Three.MeshNormalMaterial();
      //网格
      this.mesh = new Three.Mesh(geometry, material);
      // 设置位置
      // this.mesh.position.set(4,0,0);
      // 几何缩放 x轴3倍,y轴两倍，z轴1倍
      // this.mesh.scale.set(3,2,1);
      // 几何图旋转 x轴，用set也可以
      // this.mesh.rotation.x=1;
      //按网格去添加到场景上面
      this.scene.add(this.mesh);

      //添加xyz轴
      const axesHelper = new Three.AxesHelper(5);
      this.scene.add(axesHelper);

      //WebGLR渲染器 antialias 是否康齿
      this.renderer = new Three.WebGLRenderer({ antialias: true });
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);

      //轨道控制器
      var controls1 = new OrbitControls(this.camera, this.renderer.domElement);
      //监听事件
      controls1.addEventListener("change", () => {


        //渲染重构
        this.renderer.render(this.scene, this.camera);
      });
    },

    //这里好像只能用全局变量，不能用临时变量？
    //动画效果，就是会转动
    //time是当前时间 1000为1秒，用这个来实现时间卡控动画效果
    animate: function (time) {
      requestAnimationFrame(this.animate);
      //x轴自动旋转
      this.mesh.rotation.x += 0.01;
      //y轴自动旋转
      this.mesh.rotation.y += 0.02;


        // console.log(clock);
        //clock运行时间长
        // this.time = this.clock.getElapsedTime();
        //两次运行总时长
        this.daltaTime = this.clock.getDelta();
        // console.log("time:", time);
        console.log("time1:", this.daltaTime);

      // 判断是否到位置
      // if(this.mesh.position.x>4)
      // {
      //   this.mesh.position.x=0;
      // }
      // this.mesh.position.x += 0.03;
      //渲染重构
      this.renderer.render(this.scene, this.camera);
      // console.log(time);
    },
  },
  mounted() {
    this.init();
    this.animate();
  },
};
</script>
<style scoped>
#container {
  height: 100%;
}
</style>