<template>
  <div>
    <el-dialog
      title="选择图表类型"
      :visible.sync="dialogVisible"
      width="70%"
      :before-close="handleClose"
      top="10px"
    >
      <div>
        <el-input size="small" placeholder="请搜索" style="width:500px;" v-model="inputvalue"></el-input>
        <el-row style="width:100%;height:500px;overflow-y:auto">
          <el-col :span="6">
            <div style="height:100%;border-right:1px solid #ccc">
              <div style="margin-right:10px">
                <div style="height:450px;margin-top:18px;">
                  <div style="margin:5px 0;font-weight:bold;padding-left:8px">类型</div>
                  <div v-for="(item,index) in typeList" v-bind:key="index">
                    <div
                      class="tyleEach"
                      @click="clickEachType(item)"
                      :style="{background:item.clickFlag?'#ccc':''}"
                    >
                      {{ item.label }}
                      <span>({{ item.num }})</span>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </el-col>
          <el-col :span="18" style="margin-top:18px;" v-if="!lineChartFlag">
            <div class="flex-div" style="border-bottom:1px solid #ccc">
              <div class="content_right">
                <el-row style="margin-bottom:18px;">
                  <el-col :span="16">
                    <img src="./../../assets/caseExecView.png" alt width="60%" height="200px" />
                  </el-col>
                  <el-col :span="7">图说明说明说明说明说明说明说明说明说明说明</el-col>
                </el-row>
              </div>
              <div style="width:11%;margin-top:18px">
                <el-button type="primary" size="small" @click="handleConfirm">选中</el-button>
              </div>
            </div>
            <div class="flex-div" style="border-bottom:1px solid #ccc">
              <div class="content_right">
                <el-row style="margin-bottom:18px;">
                  <el-col>
                    <img src="./../../assets/platView.png" alt width="95%" height="200px" />
                  </el-col>
                  <!-- <el-col :span="16">2</el-col> -->
                </el-row>
              </div>
              <div style="width:11%;margin-top:18px">
                <el-button type="primary" size="small" @click="handleConfirm">选中</el-button>
                <el-tooltip
                  class="item"
                  effect="dark"
                  content="说明说明说明说明说明说明说明说明说明说明"
                  placement="top-start"
                >
                  <div style="font-size:18px;margin-left:20%;margin-top:10px">
                    <i class="el-icon-question"></i>
                  </div>
                </el-tooltip>
              </div>
            </div>
          </el-col>
          <el-col :span="18" style="margin-top:18px;" v-if="lineChartFlag">
            <div class="flex-div" style="border-bottom:1px solid #ccc">
              <div class="content_right">
                <el-row style="margin-bottom:18px;">
                  <el-col :span="16">
                    <img src="./../../assets/lineChart.png" alt width="60%" height="200px" />
                  </el-col>
                  <el-col :span="7">图说明说明说明说明说明说明说明说明说明说明</el-col>
                </el-row>
              </div>
              <div style="width:11%;margin-top:18px">
                <el-button type="primary" size="small" @click="handleConfirm">选中</el-button>
              </div>
            </div>
          </el-col>
        </el-row>
      </div>
      <!-- <span slot="footer" class="dialog-footer">
      <el-button size="small" @click="dialogVisible = false">取 消</el-button>
      <el-button type="primary" size="small" @click="handleConfirm">确 定</el-button>
      </span>-->
    </el-dialog>
    <el-dialog
      title="修改配置"
      :visible.sync="dialogVisibleInner"
      width="50%"
      :before-close="handleClose"
    >
      <div style="height:350px;width:100%">
        <span style="display:inline-block;width:59%;height:300px;float:left">
          <LineChart />
        </span>
        <span style="display:inline-block;width:40%;">
          <div style="margin-bottom:8px">
            标题：
            <el-input class="width_setting" size="small" />
          </div>
          <div style="margin-bottom:8px">
            类型：
            <el-select size="small" class="width_setting" v-model="caseTypeValue" placeholder="请选择">
              <el-option
                v-for="item in options_caseType"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </div>
          <div style="margin-bottom:8px">
            X轴：
            <el-select size="small" class="width_setting" v-model="Xvalue" placeholder="请选择">
              <el-option
                v-for="item in optionsX"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </div>
          <div style="margin-bottom:8px">
            Y轴：
            <el-select size="small" class="width_setting" v-model="Yvalue" placeholder="请选择">
              <el-option
                v-for="item in optionsY"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </div>
          <div style="margin-bottom:8px">
            说明：
            <el-input size="small" type="textarea" class="width_setting" />
          </div>
        </span>
      </div>

      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="dialogVisibleInner = false">取 消</el-button>
        <el-button type="primary" size="small" @click="OKFinal">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import LineChart from "../echarts/LineChart";
export default {
  name: "DialogAddGridItem",
  components: { LineChart },
  data() {
    return {
      dialogVisibleInner: false,
      lineChartFlag: false,
      inputvalue: "",
      dialogVisible: false,
      typeList: [
        {
          label: "指标卡",
          value: "1",
          num: 3,
          clickFlag: true
        },
        {
          label: "表格",
          value: "2",
          num: 2,
          clickFlag: false
        },
        {
          label: "饼图",
          value: "3",
          num: 3,
          clickFlag: false
        },
        {
          label: "折线图",
          value: "4",
          num: 5,
          clickFlag: false
        },
        {
          label: "柱状图（竖向）",
          value: "5",
          num: 6,
          clickFlag: false
        },
        {
          label: "柱状图（横向）",
          value: "6",
          num: 4,
          clickFlag: false
        },
        {
          label: "其他",
          value: "7",
          num: 0,
          clickFlag: false
        },
        {
          label: "所有",
          value: "0",
          num: 22,
          clickFlag: false
        }
      ],
      options_caseType: [
        {
          value: "1",
          label: "全部"
        },
        {
          value: "2",
          label: "手工测试"
        },
        {
          value: "3",
          label: "总自动化"
        },
        {
          value: "4",
          label: "UI自动化"
        },
        {
          value: "5",
          label: "接口自动化"
        },
        {
          value: "6",
          label: "性能自动化"
        },
        {
          value: "7",
          label: "混沌自动化"
        }
      ],
      optionsX: [
        {
          value: "1",
          label: "自然月"
        },
        {
          value: "2",
          label: "项目"
        }
      ],
      optionsY: [
        {
          value: "1",
          label: "总数"
        },
        {
          value: "2",
          label: "未解决"
        },
        {
          value: "2",
          label: "未关闭"
        }
      ],
      Xvalue: "",
      caseTypeValue: "",
      Yvalue: ""
    };
  },
  methods: {
    open() {
      this.dialogVisible = true;
    },
    handleClose() {
      this.dialogVisible = false;
    },
    clickEachType(item) {
      console.log("item--", item);
      if (item.value == "4") {
        //折线图
        this.typeList.map(item => (item.clickFlag = false));
        item.clickFlag = true;
        this.lineChartFlag = true;
      } else if (item.value == "1") {
        //指标卡
        this.typeList.map(item => (item.clickFlag = false));
        item.clickFlag = true;
        this.lineChartFlag = false;
      }
    },
    handleConfirm() {
      this.dialogVisibleInner = true;
    },
    OKFinal() {
      this.dialogVisibleInner = false;
      this.$emit("handleConfirm");
      this.dialogVisible = false;
    }
  },
  watch: {
    inputvalue() {
      this.typeList.map(item => {
        if (item.value == "0") {
          item.clickFlag = true;
        } else {
          item.clickFlag = false;
        }
      });
    }
  }
};
</script>
<style scoped>
.tyleEach {
  line-height: 25px;
  cursor: pointer;
  padding-left: 8px;
}
.tyleEach:hover {
  background: #ccc;
  /* border-radius: 3px; */
}
.flex-div {
  display: flex;
  width: auto;
}
.content_right {
  width: 89%;
  /* border: 1px solid red; */
  height: 200px;
}
.width_setting {
  width: 90%;
}
</style>