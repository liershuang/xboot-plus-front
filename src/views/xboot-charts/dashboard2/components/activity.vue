<template>
  <div class="card-activity">
    <Card>
      <p slot="title" class="card-title">最新事件</p>
      <div class="wrap">
        <Timeline>
          <TimelineItem v-for="(item, i) in data" :key="i" :color="item.color">
            <p class="time">{{ item.time }}</p>
            <p class="content">{{ item.event }}</p>
          </TimelineItem>
        </Timeline>
      </div>
    </Card>
  </div>
</template>

<script>
import { format } from "date-fns";
export default {
  name: "bar",
  components: {},
  props: {},
  data() {
    return {
      data: [
        {
          time: format(new Date(), "YYYY-MM-DD HH:mm:ss"),
          event: "用户 user1 登录了系统",
          color: "blue"
        },
        {
          time: format(new Date()-3000, "YYYY-MM-DD HH:mm:ss"),
          event: "用户 admin1234 支付了订单，包含2件商品，共计￥123.12元",
          color: "red"
        },
        {
          time: format(new Date()-15000, "YYYY-MM-DD HH:mm:ss"),
          event: "用户 13699345246 登录了系统",
          color: "blue"
        },
        {
          time: format(new Date()-23000, "YYYY-MM-DD HH:mm:ss"),
          event: "用户 18823451373 提交了订单，包含1件商品，共计￥88.39元",
          color: "green"
        },
        {
          time: format(new Date()-30000, "YYYY-MM-DD HH:mm:ss"),
          event: "用户 请输入昵称 分享了商品 Apple Watch Series 5 黑色 蜂窝",
          color: "green"
        },
        {
          time: format(new Date()-50000, "YYYY-MM-DD HH:mm:ss"),
          event: "用户 请输入昵称 登录了系统",
          color: "blue"
        },
      ]
    };
  },
  methods: {
    init() {}
  },
  mounted() {
    this.init();
  }
};
</script>
<style lang="less">
.card-activity {
  .ivu-card-head {
    border-bottom: none !important;
  }
  .card-title {
    color: #67757c;
  }
  .wrap {
    overflow: auto;
    height: 368px;
  }
}
</style>