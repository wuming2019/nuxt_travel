<template>
  <div style="padding:50px;margin-bottom:20px;">
    <div class="tabs">
      <span
        v-for="(item,index) in ['新闻','体育','娱乐']"
        :key="index"
        @click="handleClick(index)"
        :class="{active: current === index}"
      >{{item}}</span>
    </div>

    <div v-show="current === 0">
      <ul>
        <li>春风宿雪多年</li>
        <li>春风宿雪多年</li>
        <li>春风宿雪多年</li>
      </ul>
    </div>
    <div v-show="current === 1">
      <ul>
        <li>拎着钓叟的鱼弦</li>
        <li>拎着钓叟的鱼弦</li>
        <li>拎着钓叟的鱼弦</li>
      </ul>
    </div>
    <div v-show="current === 2">
      <ul>
        <li>问卧龙几两钱</li>
        <li>问卧龙几两钱</li>
        <li>问卧龙几两钱</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 记录当前的状态
      current: 0
    };
  },
  methods: {
    handleClick(index) {
      // 修改current
      this.current = index;
    }
  }
};
</script>

<style lang="less" scoped>
.tabs {
  margin-bottom: 28px;

  span {
    cursor: pointer;
  }
}
.active {
  color: purple;
}
</style>