<template>
  <div class="title_top">
    <span class="text">{{text}}</span>
    <span class="edit">
      <i class="el-icon-edit" @click="editCard"></i>
    </span>
    <span class="remove" @click="$emit('removeItem', text)">x</span>
  </div>
</template>
<style>
.remove {
  position: absolute;
  right: 2px;
  top: 0;
  cursor: pointer;
}
.edit {
  position: absolute;
  right: 15px;
  top: 0;
  cursor: pointer;
}
</style>
<script>
export default {
  name: "TestElement",
  props: {
    text: {
      type: String,
      default: "x"
    }
  },
  data: function() {
    return {};
  },
  mounted: function() {
    console.log("### " + this.text + " ready!");
  },
  methods: {
    editCard() {
      this.$emit("editCard", this.text);
    }
  }
};
</script>
<style scoped>
.title_top {
  border: 1px solid #337dff;
  height: 27px;
  background: #aecafa;
}
.text {
  font-size: 14px;
}
.remove {
  font-size: 14px;
  line-height: 27px;
  margin-right: 4px;
}
.edit {
  font-size: 14px;
  line-height: 27px;
  margin-right: 4px;
}
</style>
