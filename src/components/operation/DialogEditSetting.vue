<template>
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
</template>


<script>
import LineChart from "../echarts/LineChart";
export default {
  name: "DialogEditSetting",
  components: { LineChart },
  data() {
    return {
      dialogVisibleInner: false,
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
      this.dialogVisibleInner = true;
    },
    handleClose() {
      this.dialogVisibleInner = false;
    },
    OKFinal() {
      this.dialogVisibleInner = false;
      this.$emit("OKFinal");
    }
  }
};
</script>
<style scoped>
.width_setting {
  width: 90%;
}
</style>