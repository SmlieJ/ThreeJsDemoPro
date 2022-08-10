<template>
  <div>
    <div id="container"></div>
  </div>
</template>
 
<script>
//这里是基础的说明，包括几何体的位移、旋转、缩放等，还有动画特效的时间
import * as Three from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
//导入动画库
import gsap from "gsap";
// 导入data.gui
import * as dat from "dat.gui";
// 几何、场景、控制器必须放到外面
let scene;
let mesh;
let controls;
//时间触发器
// let clock = new Three.Clock();
let time;
let daltaTime;
let controls1;
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
        0.5,
        10
      );
      //设置相机位置
      // this.camera.position.z = 3;
      this.camera.position.set(5, 5, 5);
      //场景
      this.scene = new Three.Scene();
      //箱子几何
      let geometry = new Three.BoxGeometry(2, 2, 2);
      //材质 有不同的 MeshLambertMaterial MeshNormalMaterial 要去官方，有些赋值不了颜色
      let material = new Three.MeshNormalMaterial({ color: "#0000ff" });
      //网格
      this.mesh = new Three.Mesh(geometry, material);   
      console.log("material", material);
      console.log("mesh", this.mesh);   
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
      this.controls1 = new OrbitControls(this.camera, this.renderer.domElement);
      //设置控制器阻尼，让控制器更有真实效果，必须在动画循环里调用.update() 动画慢慢停下
      this.controls1.enableDamping = true;

      //设置一个控制台，然后来改变属性，不过动画要暂停或者停止，不然有冲突
      // step 间隔
      const gui = new dat.GUI();
      // gui.add(this.mesh.position, "x").min(0).max(5).step(0.1).name("平移");
      // gui
      //   .add(this.mesh.rotation, "x")
      //   .min(0)
      //   .max(5)
      //   .step(0.1)
      //   .name("旋转")
      //   .onChange((value) => {
      //     console.log("值被修改：", value);
      //   })
      //   .onFinishChange((value) => {
      //     console.log("没有修改了", value);
      //   });
      const params = { color: "#ffff00" };
      //修改颜色 不过这个是设定元素，这个应该要绑定
      gui.addColor(params, "color").onChange((value) => {
        // console.log("值被修改：", value);
        // console.log(this.mesh.material);
        this.mesh.material.color.set(value);
      });

      // gsap 设置动画 第一个参数动画效果，第二参数 位移的方向，第三 参数 花费时间
      // 用这个，就不用
      // greensock.com/get-started/#easing 页面生成动画
      // onComplete 动画完成执行
      // onStart 动画开始特效
      // repeat 重复次数,其实是3次，还有本身一次，-1是无限次
      // yoyo 往返移动
      // delay 延迟时间运动
      const animate1 = gsap.to(this.mesh.position, {
        x: 5,
        duration: 5,
        repeat: -1,
        yoyo: true,
        delay: 2,
        // onComplete: () => {
        //   console.log("动画完成");
        // },
        // onStart: () => {
        //   console.log("动画开始");
        // },
      });

      const animate2 = gsap.to(this.mesh.rotation, {
        x: 5,
        duration: 5,
        repeat: -1,
        yoyo: true,
        delay: 2,
        // onComplete: () => {
        //   console.log("动画完成");
        // },
        // onStart: () => {
        //   console.log("动画开始");
        // },
      });

      //更新渲染画面 页面宽高变化
      window.addEventListener("resize", () => {
        // console.log("画面变化了");
        // 更新摄像头
        this.camera.aspect = window.innerWidth / window.innerHeight;
        // 更新摄像机的投影矩阵
        this.camera.updateProjectionMatrix();
        // 更新渲染器
        this.renderer.setSize(window.innerWidth, window.innerHeight);
        // 设置渲染器的像素比
        this.renderer.setPixelRatio(window.devicePixelRatio);
      });

      // 双击效果，点击是不是click?
      window.addEventListener("dblclick", () => {
        // 判断是否运动 暂停和恢复 指定的动画特效
        if (animate1.isActive()) {
          animate2.pause();
          animate1.pause();
        } else {
          animate2.resume();
          animate1.resume();
        }
        // 双击控制屏幕进入全屏，退出全屏 这个慢慢找吧
      });
      // 监听事件
      this.controls1.addEventListener("change", () => {
        //渲染重构
        this.renderer.render(this.scene, this.camera);
      });
    },

    //这里好像只能用全局变量，不能用临时变量？
    //动画效果，就是会转动
    //time是当前时间 1000为1秒，用这个来实现时间卡控动画效果
    //动画，每一帧渲染
    animate: function (time) {
      this.controls1.update();
      requestAnimationFrame(this.animate);
      //x轴自动旋转
      // this.mesh.rotation.x += 0.01;
      //y轴自动旋转
      // this.mesh.rotation.y += 0.02;

      // console.log(clock);
      //clock运行时间长
      // this.time = this.clock.getElapsedTime();
      //两次运行总时长
      // this.daltaTime = this.clock.getDelta();
      // console.log("time:", time);

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
  height: 1000px;
}
</style>