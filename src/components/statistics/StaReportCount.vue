
<template>
  <div style="margin-top: 10px">
    <el-tabs v-model="defaultTab" type="card">
      <el-tab-pane label="按组员统计" name="depMana">
        <dep-mana></dep-mana>
      </el-tab-pane>
      <el-tab-pane label="按地区统计" name="positionMana">
        <pos-mana state="position"></pos-mana>
      </el-tab-pane>
    </el-tabs>
  </div>
</template>
<script>
  import DepMana from './basic/TaskReportByUser.vue'
  import PosMana from './basic/TaskReportBydept.vue'
  export default {
    data() {
      return {
        defaultTab: 'depMana'
      };
    },
    methods: {},
    components: {
      'dep-mana': DepMana,
      'pos-mana': PosMana
    }
  };
</script>
<style>
  .vue-treeselect__control {
    padding-left: 5px;
    padding-right: 5px;
    display: table;
    table-layout: fixed;
    width: 100%;
    height: 28px;

  }

</style>
