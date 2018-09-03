<template>
    <div class="lucky-wheel">
        <div class="wheel-title"></div>
        <div class="wheel-main">
            <div class="wheel-pointer" @click="rotateHandle()"></div>
            <div class="prize-list" :style="{transform, transition}">
              <template v-for="(item, index) in prizeList">
                <div class="prize-item" :key="index" :style="{transform: getRotate(index)}">
                    <img class="prize-pic" :src="item.icon">
                    <div class="prize-count" v-if="item.count" v-text="item.count"></div>
                    <div class="prize-type" v-text="item.name"></div>
                </div>
              </template>
            </div>
        </div>
        <div class="lucky-tip">今日免费抽奖次数： {{luckyCount}}</div>
        <div class="rules">
            <div class="rule-title">活动规则</div>
            <ol class="rule-content">
                <li>每日签到后，即可获得一次幸运大转盘的机会，仅限当天有效，过期作废。</li>
                <li>金币抽奖，每10个金豆可以兑换一次大转盘抽奖机会。</li>
                <li>所中金豆或积分到【我的账户】中查询。累计达到100金豆及以上，可以兑换相应奖品。</li>
            </ol>
        </div>
        <template v-if="showToast">
          <div class="toast">
            <img v-if="resultPrize.isPrize" src="../assets/img/congratulation.png" class="toast-picture">
            <img v-else src="../assets/img/sorry.png" class="toast-picture">
            <div class="close" @click="closeToast()"></div>
            <div class="toast-title">{{tips || toastTitle}}</div>
            <div class="toast-btn" @click="closeToast">关闭</div>
          </div>
          <div class="toast-mask"></div>
        </template>
    </div>
</template>
<script>
export default {
  data() {
    return {
      luckyCount: 0, //抽奖次数
      prizeList: [], //奖品列表
      showToast: false, //抽奖结果弹出框控制器
      currentRotat: 0, //初始旋转角度
      transition: '',
      transform: 0,
      canClick: true, // 是否可以旋转抽奖
      resultIndex: -1, // 最终要旋转到哪一块，对应 prizeList 的下标
      tips: ''
    };
  },
  mounted() {
    this.initLuckyCount();
    this.initPrizeList();
  },
  computed: {
    resultPrize() {
      return this.prizeList[this.resultIndex] || {};
    },
    toastTitle() {
      return this.resultPrize.isPrize
        ? "恭喜您，获得 " + this.resultPrize.count + ' ' + this.resultPrize.name
        : "未中奖";
    }
  },
  methods: {
    getRotate(index, circle = 0) {
      var deg = (index + 0.5) * 360 / this.prizeList.length;
      if (circle) { // 旋转是加上之前已经旋转的度数
        this.currentRotat = deg = this.currentRotat - this.currentRotat % 360 + circle * 360 - deg;
      }
      return 'rotate(' + deg + 'deg)';
    },
    initLuckyCount() {
      // TODO 根据接口获取当前可抽取次数
      this.luckyCount = 3;
    },
    initPrizeList() {
      // TODO 根据接口获取奖品数据
      this.prizeList = [
        {
          icon: require("../assets/img/bean_500.png"), // 奖品图片
          count: 10, // 奖品数量
          name: "易趣豆", // 奖品名称
          isPrize: 1 // 该奖项是否为奖品
        },
        {
          icon: require("../assets/img/bean_five.png"),
          count: 5,
          name: "豆",
          isPrize: 1
        },
        {
          icon: require("../assets/img/bean_one.png"),
          count: 10,
          name: "易趣豆",
          isPrize: 1
        },
        {
          icon: require("../assets/img/point_five.png"),
          count: 5,
          name: "积分",
          isPrize: 1
        },
        {
          icon: require("../assets/img/point_ten.png"),
          count: 10,
          name: "积分",
          isPrize: 1
        },
        {
          icon: require("../assets/img/bean_500.png"),
          count: 30,
          name: "易趣豆",
          isPrize: 1
        },
        {
          icon: require("../assets/img/give_up.png"),
          count: 0,
          name: "未中奖",
          isPrize: 0
        },
        {
          icon: require("../assets/img/bean_500.png"),
          count: 20,
          name: "易趣豆",
          isPrize: 1
        }
      ];
    },
    joinLucky() {
      this.transition = 'transform 3s ease-in';
      this.transform = this.getRotate(0.5, 3); // 附加多转几圈，2-3
      // 模拟 Ajax 请求
      setTimeout(() => {
        // TODO 调用后端接口进行抽奖，判断中了哪个奖
        this.resultIndex = Math.round(Math.random() * (this.prizeList.length - 1));
        this.transition = 'transform 3s ease-out';
        this.transform = this.getRotate(this.resultIndex, 3);
        setTimeout(() => { // 等待旋转动画结束后，允许再次触发
          this.luckyCount--;
          this.canClick = this.showToast = true;
        }, 3500)
      }, 2000); // 延时，保证转盘转完
    },
    rotateHandle() {
      if (!this.canClick) return;
      if (this.luckyCount < 1) {
        this.resultIndex = -1;
        this.tips = '抽奖次数已经用完！';
        this.showToast = true;
        return;
      }
      this.canClick = false; // 旋转结束前，不允许再次触发
      this.joinLucky();
    },
    //关闭弹窗
    closeToast() {
      this.showToast = false;
    }
  }
};
</script>
<style lang="less">
.lucky-wheel {
  position: relative;
  margin: 0 auto;
  padding-top: 30px;
  text-align: center;
  width: 100%;
  max-width: 740px;
  background: #fccf85 url("../assets/img/color_pillar.png") no-repeat center bottom;
  background-size: 100% 100%;
  .wheel-title {
    margin: 0 auto;
    width: 400px;
    max-width: 100%;
    height: 100px;
    background: url("../assets/img/lucky_title.png") no-repeat center top;
    background-size: 100%;
  }
  .wheel-main {
    position: relative;
    margin: 0 auto;
    width: 300px;
    height: 300px;
    overflow: hidden;
    background-size: 100%;
  }
  .wheel-pointer {
    position: absolute;
    top: 50%;
    left: 50%;
    z-index: 100;
    transform: translate(-50%, -50%);
    width: 90px;
    height: 90px;
    background: url("../assets/img/draw_btn.png") no-repeat center top;
    background-size: 100%;
  }
  .prize-list {
    position: relative;
    width: 100%;
    height: 100%;
    background: url("../assets/img/draw_wheel.png") no-repeat center top;
    background-size: 100% 100%;
    color: #fff;
    font-weight: 500;
    text-align: center;
    .prize-item {
      position: absolute;
      bottom: 50%;
      left: 50%;
      margin-left: -40px;
      width: 80px;
      height: 150px;
      line-height: 12px;
      z-index: 2;
      transform-origin: center bottom;
    }
  }

  .prize-pic {
    height: 40px;
    margin: 20px auto 0;
    display: inline-block;
  }
  .prize-count, .prize-type {
    font-size: 12px;
  }
  .rules {
    position: relative;
    width: 100%;
    margin: -2px auto;
    background-color: #f36d56;
    padding-bottom: 30px;
  }
  .lucky-tip {
    padding-top: 56px;
    height: 100px;
    background: transparent url("../assets/img/luck_bg.png") no-repeat center top;
    background-size: 100% 100%;
    font-size: 14px;
    color: #ffeb39;
  }
  .rule-title {
    display: inline-block;
    color: #fccc6e;
    padding: 10px;
    background-color: #f36d56;
    position: relative;
    z-index: 1;
  }
  .rule-content {
    margin: -20px 20px 0;
    padding: 20px;
    border: 1px solid #fbc27f;
    font-size: 12px;
    color: #fff8c5;
    line-height: 24px;
    text-align: left;
    li {
      list-style: decimal;
    }
  }
  .toast-mask {
    position: fixed;
    top: 0;
    left: 0;
    background-color: rgba(0, 0, 0, 0.6);
    z-index: 10000;
    width: 100%;
    height: 100%;
  }
  .toast {
    position: fixed;
    top: 50%;
    left: 50%;
    z-index: 20000;
    margin: -80px auto auto -130px;
    width: 260px;
    border-radius: 10px;
    border: 1px dotted #fccc6e;
    background-color: #fcf4e0;
  }
  .toast-picture {
    margin-top: -70px;
    width: 300px;
  }
  .toast-title {
    padding: 20px 0;
    font-size: 18px;
    color: #fc7939;
    text-align: center;
  }
  .toast-btn {
    margin: 0 auto 20px;
    width: 70px;
    background-color: #f29455;
    box-shadow: 0px 4px 0px 0px rgba(174, 34, 5, 0.7);
    border-radius:5px;
    text-align: center;
    line-height: 30px;
    color: #fff;
  }
  .close {
    position: absolute;
    top: -20px;
    right: -20px;
    width: 30px;
    height: 30px;
    background: url("../assets/img/close_store.png") no-repeat center top;
    background-size: 100%;
  }
}
</style>
