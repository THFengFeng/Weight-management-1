<template>
  <Layout class-prefix="layout">
    <NumPad :value.sync="record.amount" @submit="onSaveRecord"/>
    <!--    <Tab :value.sync="record.type" :data-source="typeList"/>-->
    <div class="createdAt">
      <FormItem :value.sync="record.createdAt" class="datePicker" field-name="日期选择" type="date"/>
    </div>
    <div class="notes">
      <FormItem :value.sync="record.notes" field-name="备注" placeholder="每日一问"/>
    </div>
    <Tags :value.sync="record.tag"/>
  </Layout>
</template>

<script lang="ts">
  import Vue from "vue";
  import {Component, Watch} from "vue-property-decorator";
  import NumPad from "@/components/Money/NumPad.vue";
  import FormItem from "@/components/Money/FormItem.vue";
  import Tags from "@/components/Money/Tags.vue";
  import Button from "@/components/Button.vue";
  import typeList from "@/lib/typeList";
  import dayjs from "dayjs";

  @Component({
    components: {Button, FormItem, NumPad, Tags},
  })
  export default class Money extends Vue {
    typeList = typeList;
    record: RecordItem = {
      amount: 0,
      type: "-",
      notes: "",
      tag: {id: "", name: ""},
      createdAt: dayjs(new Date()).format("YYYY-MM-DD")
    };

    get tagList() {
      // [{"id":"1","name":"空腹"},{"id":"2","name":"饱腹"},{"id":"3","name":"运动后"}]
      return this.$store.state.tagList;
    }

    get recordList() {
      return this.$store.state.recordList;
    }

    // 创建时获取记录
    created() {
      this.$store.commit("fetchTags");
      this.$store.commit("fetchRecords");
      this.record.tag = this.tagList[0];
    }

    testDuplicate(record: RecordItem) {
      let flag = false;
      this.recordList.forEach((r: RecordItem) => {
        if (r.createdAt === record.createdAt && JSON.stringify(r.tag) === JSON.stringify(record.tag)) {
          flag = true;
        }
      });
      return flag;
    }

    onSaveRecord() {
      if (this.testDuplicate(this.record)) {
        window.alert("已记录过了");
      } else if (this.record.amount <= 40) {
        window.alert("每天不吃饭么？");
      } else if (this.record.amount > 100) {
        window.alert("这么重？你还是个人？");
      } else {
        this.$store.commit("createRecord", this.record);
        window.alert("体重记录成功");
      }
    }

    @Watch("recordList")
    onRecordListChange() {
      this.$store.commit("saveRecords");
    }
  }
</script>

<style lang="scss">

  .layout-content {
    display: flex;
    flex-direction: column-reverse;
  }

  .notes {
    padding: 12px 0;
  }

  .createdAt {
    position: fixed;
    top: 0;
    width: 100vw;
    max-width: 500px;
    background-image: linear-gradient(to right, #05F9B5 0%, #20F905 100%);;
    color: #000000;
  }

</style>

<style lang="scss" scoped>
  @import ".././assets/styles/helper.scss";
</style>

