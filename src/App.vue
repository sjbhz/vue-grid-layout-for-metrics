<template>
  <div id="app">
    <h1 style="text-align: center">度量配置</h1>
    <!--<pre>{{ layout | json }}</pre>-->
    <div>
      <!-- <div class="layoutJSON">
        Displayed as
        <code>[x, y, w, h]</code>:
        <div class="columns">
          <div class="layoutItem" v-for="item in layout" :key="item.i">
            <b>{{item.i}}</b>
            : [{{item.x}}, {{item.y}}, {{item.w}}, {{item.h}}]
          </div>
        </div>
      </div>-->

      <!--<div class="layoutJSON">
                Displayed as <code>[x, y, w, h]</code>:
                <div class="columns">
                    <div class="layoutItem" v-for="item in layout2">
                        <b>{{item.i}}</b>: [{{item.x}}, {{item.y}}, {{item.w}}, {{item.h}}]
                    </div>
                </div>
      </div>-->
    </div>
    <div id="content">
      <div v-if="showBtn" style="border:1px solid #ccc;padding:7px 0 0 5px;">
        <!-- <button @click="decreaseWidth">Decrease Width</button>
        <button @click="increaseWidth">Increase Width</button>-->
        <!-- <button @click="scaleHalf">Scale x0.5</button> -->
        <!-- <button @click="scaleThreeQuarters">Scale x0.75</button> -->
        <!-- <button @click="scaleIdentity">Scale x1.0</button> -->
        <!-- <button @click="addItem">Add an item</button> -->
        <!-- Add to show rtl support -->
        <!-- <button @click="changeDirection">Change Direction</button> -->
        <input type="checkbox" v-model="draggable" /> Draggable(可移动)
        <input type="checkbox" v-model="resizable" /> Resizable(可拖拽)
        <input type="checkbox" v-model="mirrored" /> Mirrored(RTL/LTR 的转换)
        <!-- <input type="checkbox" v-model="bounded" /> Bounded -->
        <!-- <input type="checkbox" v-model="responsive" /> Responsive(布局是否应响应窗口宽度,建议不要使用 会引起布局紊乱) -->
        <!-- <input type="checkbox" v-model="preventCollision" /> Prevent Collision (图表渲染会出现影响) -->
        <input type="checkbox" v-model="compact" /> Vertical Compact(垂直方向紧凑布局)
        <div style="margin-top: 10px;margin-bottom: 10px;">
          <button @click="addItemDynamically" style="margin-right:10px">动态添加一项</button>
          <button @click="handleLab">查看指标库</button>
        </div>
        <!-- <div style="margin-top: 10px;margin-bottom: 10px;">
          Row Height:
          <input type="number" v-model="rowHeight" /> Col nums:
          <input type="number" v-model="colNum" />
          Margin x:
          <input type="number" v-model="marginX" /> Margin y:
          <input type="number" v-model="marginY" />
        </div>-->
      </div>
      <button @click="showBtn=!showBtn" v-if="!showBtn">展开操作栏</button>
      <button @click="handlePreView" v-if="showBtn">隐藏操作栏并预览</button>
      <grid-layout
        id="grid-layout"
        :margin="[parseInt(marginX), parseInt(marginY)]"
        :layout.sync="layout"
        :col-num="parseInt(colNum)"
        :row-height="rowHeight"
        :is-draggable="draggable"
        :is-resizable="resizable"
        :is-mirrored="mirrored"
        :is-bounded="bounded"
        :prevent-collision="preventCollision"
        :vertical-compact="compact"
        :restore-on-drag="restoreOnDrag"
        :use-css-transforms="true"
        :responsive="responsive"
        :transformScale="transformScale"
        @layout-created="layoutCreatedEvent"
        @layout-before-mount="layoutBeforeMountEvent"
        @layout-mounted="layoutMountedEvent"
        @layout-ready="layoutReadyEvent"
        @layout-updated="layoutUpdatedEvent"
        @breakpoint-changed="breakpointChangedEvent"
      >
        <grid-item
          v-for="item in layout"
          :key="item.i"
          :static="item.static"
          :x="item.x"
          :y="item.y"
          :w="item.w"
          :h="item.h"
          :i="item.i"
          :min-w="item.minW"
          :max-w="item.maxW"
          :min-x="item.minX"
          :max-x="item.maxX"
          :min-y="item.minY"
          :max-y="item.maxY"
          :preserve-aspect-ratio="item.preserveAspectRatio"
          @resize="resize"
          @move="move"
          @resized="resized"
          @container-resized="containerResized"
          @moved="moved"
        >
          <!--<custom-drag-element :text="item.i"></custom-drag-element>-->
          <test-element
            :text="item.i"
            @removeItem="removeItem($event)"
            @editCard="editCard($event)"
          ></test-element>
          <template #chart>
            <BarChartsDemo :ref="`RefChart${item.i}`" v-if="item.i=='6' || item.i=='缺陷趋势' " />
            <!-- 当 grid-item 改变时执行 SizeAutoChange() 函数  -->
            <LineChart :ref="`RefChart${item.i}`" v-else />
            <!-- 设定好 ref 使得下面操控的是某个子组件的函数 -->
          </template>
          <!--<button @click="clicked">CLICK ME!</button>-->
        </grid-item>
      </grid-layout>
      <hr />
      <!--<grid-layout
                    :layout="layout2"
                    :col-num="12"
                    :row-height="rowHeight"
                    :is-draggable="draggable"
                    :is-resizable="resizable"
                    :vertical-compact="true"
                    :use-css-transforms="true"
            >
                <grid-item v-for="item in layout2" :key="item.i"
                           :x="item.x"
                           :y="item.y"
                           :w="item.w"
                           :h="item.h"
                           :min-w="2"
                           :min-h="2"
                           :i="item.i"
                           :is-draggable="item.draggable"
                           :is-resizable="item.resizable"
                >
                    <test-element :text="item.i"></test-element>
                </grid-item>
      </grid-layout>-->
    </div>

    <DialogAddGridItem ref="dialogAddGridItem" @handleConfirm="handleConfirm" />
    <DialogManageGridItem ref="dialogManageGridItem" @handleEdit="handleEdit" />
    <DialogEditSetting ref="dialogEditSetting" @OKFinal="OKFinal" />
  </div>
</template>

<script>
import GridItem from "./components/GridItem.vue";
import GridLayout from "./components/GridLayout.vue";
// import ResponsiveGridLayout from './components/ResponsiveGridLayout.vue';
import TestElement from "./components/TestElement.vue";
import BarChartsDemo from "./components/echarts/BarDemoCharts";
import LineChart from "./components/echarts/LineChart";
import DialogAddGridItem from "./components/operation/DialogAddGridItem";
import DialogManageGridItem from "./components/operation/DialogManageGridItem";
import DialogEditSetting from "./components/operation/DialogEditSetting";

import CustomDragElement from "./components/CustomDragElement.vue";
import { getDocumentDir, setDocumentDir } from "./helpers/DOM";
//var eventBus = require('./eventBus');

//GridItem属性
// i:栅格中元素的ID
// x:位于第几列  y:位于第几行  w:初始宽度 h:初始高度
// minW:最小宽度，值为colWidth的倍数          maxW:最大宽度
// minH:元素的最小高度，值为rowHeight的倍数   maxH:最大高度
// isDraggable:可拖拽。如果值为null则取决于父容器
// isResizable:可调整大小。如果值为null则取决于父容器
// static:为静态的（无法拖拽、调整大小或被其他元素移动）

// 以下属性值为css-like选择器：
// dragIgnoreFrom：哪些子元素无法触发拖拽事件  default: 'a, button'
// dragAllowFrom:哪些子元素可以触发拖拽事件，为null则表示所有子元素（dragIgnoreFrom的除外）default: null
// resizeIgnoreFrom:哪些子元素无法触发调整大小的事件  default: 'a, button'

let testLayout = [
  {
    x: 0,
    y: 0,
    w: 6,
    h: 5,
    i: "缺陷现状概览",
    resizable: true,
    draggable: true,
    static: false,
    minY: 0,
    maxY: 2
  },
  {
    x: 6,
    y: 0,
    w: 6,
    h: 5,
    i: "缺陷修复效率概览",
    resizable: null,
    draggable: null,
    static: false
  },
  {
    x: 0,
    y: 5,
    w: 6,
    h: 5,
    i: "存量缺陷按项目排名",
    resizable: false,
    draggable: false,
    static: false,
    minX: 4,
    maxX: 4,
    minW: 2,
    maxW: 2,
    preserveAspectRatio: true
  },
  {
    x: 6,
    y: 5,
    w: 6,
    h: 5,
    i: "存量缺陷占比",
    resizable: false,
    draggable: false,
    static: false,
    preserveAspectRatio: true
  },
  {
    x: 0,
    y: 10,
    w: 6,
    h: 5,
    i: "存量缺陷按成员排名",
    resizable: false,
    draggable: false,
    static: false
  },
  {
    x: 6,
    y: 10,
    w: 6,
    h: 5,
    i: "缺陷趋势",
    resizable: false,
    draggable: false,
    static: false
  },
  {
    x: 0,
    y: 15,
    w: 6,
    h: 5,
    i: "6",
    resizable: false,
    draggable: false,
    static: false
  }
];

export default {
  name: "app",
  components: {
    GridLayout,
    GridItem,
    TestElement,
    BarChartsDemo,
    LineChart,
    DialogAddGridItem,
    DialogManageGridItem,
    DialogEditSetting,
    CustomDragElement
  },
  data() {
    return {
      showBtn: true, //展示操作

      layout: JSON.parse(JSON.stringify(testLayout)),
      layout2: JSON.parse(JSON.stringify(testLayout)),
      //GridLayout属性
      draggable: true, //可拖拽
      resizable: true, //可调整大小
      mirrored: false, //栅格中的元素是否可镜像反转
      responsive: true, //布局是否为响应式
      bounded: false,
      transformScale: 1,
      preventCollision: false, //防止碰撞属性，值设置为ture时，栅格只能拖动至空白处
      compact: true,
      restoreOnDrag: true,
      rowHeight: 50, //每行的高度，单位像素
      colNum: 12, //栅格系统的列数
      index: 0,
      marginX: 10,
      marginY: 10
    };
  },
  mounted: function() {
    this.index = this.layout.length;
  },
  methods: {
    handlePreView() {
      this.showBtn = !this.showBtn;
      this.draggable = false;
      this.resizable = false;
    },
    clicked: function() {
      window.alert("CLICK!");
    },
    increaseWidth: function() {
      let width = document.getElementById("content").offsetWidth;
      width += 20;
      document.getElementById("content").style.width = width + "px";
    },
    decreaseWidth: function() {
      let width = document.getElementById("content").offsetWidth;
      width -= 20;
      document.getElementById("content").style.width = width + "px";
    },
    scaleHalf: function() {
      this.transformScale = 0.5;
      document.getElementById("grid-layout").style.transform = "scale(0.5)";
    },
    scaleThreeQuarters: function() {
      this.transformScale = 0.75;
      document.getElementById("grid-layout").style.transform = "scale(0.75)";
    },
    scaleIdentity: function() {
      this.transformScale = 1;
      document.getElementById("grid-layout").style.transform = "scale(1)";
    },
    removeItem: function(i) {
      console.log("### REMOVE " + i);
      const index = this.layout.map(item => item.i).indexOf(i);
      this.layout.splice(index, 1);
    },
    editCard: function(i) {
      console.log("### editCard " + i);
      this.$refs.dialogEditSetting.open();
    },
    OKFinal: function() {
      console.log("### OKFinal ");
    },
    addItem: function() {
      // let self = this;
      //console.log("### LENGTH: " + this.layout.length);
      let item = {
        x: 0,
        y: 0,
        w: 6,
        h: 5,
        i: this.index + "",
        whatever: "bbb"
      }; //新建的位于左上角
      this.index++;
      this.layout.push(item);
    },
    //指标库
    handleLab() {
      this.$refs.dialogManageGridItem.open();
    },
    //编辑指标库
    handleEdit() {},
    // 添加选中指标图表
    handleConfirm() {
      this.addItemDynamically0();
    },

    addItemDynamically() {
      this.$refs.dialogAddGridItem.open();
    },
    //直接动态添加一项
    addItemDynamically0: function() {
      const x = (this.layout.length * 2) % (this.colNum || 12);
      const y = this.layout.length + (this.colNum || 12);
      console.log("X=" + x + " Y=" + y);
      let item = {
        x: x,
        y: y,
        w: 6,
        h: 5,
        i: this.index + ""
      };
      this.index++;
      this.layout.push(item);
    },
    move: function(i, newX, newY) {
      console.log("MOVE i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    SizeAutoChange(ChartId) {
      var EchartId = `RefChart${ChartId}`; // 拿到子组件实例对象
      eval(`this.$refs.${EchartId}[0].sizechange()`); //执行子组件的函数
      // console.log("EchartIdLine----", EchartIdLine);
    },
    resize: function(i, newH, newW, newHPx, newWPx) {
      this.SizeAutoChange(i);
      console.log(
        "RESIZE i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    moved: function(i, newX, newY) {
      console.log("### MOVED i=" + i + ", X=" + newX + ", Y=" + newY);
    },
    resized: function(i, newH, newW, newHPx, newWPx) {
      console.log(
        "### RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    containerResized: function(i, newH, newW, newHPx, newWPx) {
      console.log(
        "### CONTAINER RESIZED i=" +
          i +
          ", H=" +
          newH +
          ", W=" +
          newW +
          ", H(px)=" +
          newHPx +
          ", W(px)=" +
          newWPx
      );
    },
    /**
     * Add change direction button
     */
    changeDirection() {
      let documentDirection = getDocumentDir();
      let toggle = "";
      if (documentDirection === "rtl") {
        toggle = "ltr";
      } else {
        toggle = "rtl";
      }
      setDocumentDir(toggle);
      //eventBus.$emit('directionchange');
    },
    // 布局创建事件
    layoutCreatedEvent: function(newLayout) {
      console.log("Created layout: ", newLayout);
    },
    // 布局安装以前事件
    layoutBeforeMountEvent: function(newLayout) {
      console.log("beforeMount layout: ", newLayout);
    },
    // 布局安装事件
    layoutMountedEvent: function(newLayout) {
      console.log("Mounted layout: ", newLayout);
    },
    // 布局准备活动
    layoutReadyEvent: function(newLayout) {
      console.log("Ready layout: ", newLayout);
    },
    // 格子位置 大小更新
    layoutUpdatedEvent: function(newLayout) {
      console.log("Updated layout: ", newLayout);
    },
    breakpointChangedEvent: function(newBreakpoint, newLayout) {
      console.log(
        "breakpoint changed breakpoint=",
        newBreakpoint,
        ", layout: ",
        newLayout
      );
    }
  }
};
</script>

<style>
/*    #app {
            font-family: 'Avenir', Helvetica, Arial, sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            text-align: center;
            color: #2c3e50;
            margin-top: 60px;
        }

        h1, h2 {
            font-weight: normal;
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
        }*/
</style>

<style lang="css" >
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /*text-align: center;*/
  color: #2c3e50;
  /*margin-top: 60px;*/
}
</style>

