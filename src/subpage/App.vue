<template>
  <div class="page" id="app">
    <div class="header" >
      <h3>买卖交易</h3>
    </div>
    <div class="content">
      <form action="" >
        <ul class="list">
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                现价
              </div>
              <div class="list-item-right">
                <input type="number" min=0 v-auto-clear class="form-control" v-model="currentPrice" @change="countCurrency(0)" placeholder="请输入股票现价">
              </div>
            </div>
          </li>
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                涨跌幅度（%）：
              </div>
              <div class="list-item-body">
                <input type="number" min=0 v-auto-clear class="form-control" placeholder="请输入股价涨跌幅" @change="countCurrency(0)" v-model="per">
              </div>
              <div class="list-item-right">
                <span class="counter" @click="countCurrency(1)">+</span><span class="counter" @click="countCurrency(-1)">-</span>
              </div>
            </div>
          </li>
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                精度：
              </div>
              <div class="list-item-right">
                <ul class="tabbar">
                  <li :class="{'active': radio == 1}" @click="radio = 1;countCurrency(0)"><a>1</a></li>
                  <li :class="{'active': radio == 0.2}" @click="radio = 0.2;countCurrency(0)"><a>0.2</a></li>
                </ul>
              </div>
            </div>
          </li>
        </ul>
        <div class="list">
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                挂单买价 ：
              </div>
              <div class="list-item-right">
                <input type="text" disabled class="form-control" v-model="buyPrice">
              </div>
            </div>
          </li>
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                挂单卖价：
              </div>
              <div class="list-item-right">
                <input type="text" disabled class="form-control" v-model="sellPrice">
              </div>
            </div>
          </li>
        </div>
        <ul class="list">
            <li class="list-item">
              <div class="list-item-inner">
                <div class="list-item-left">
                  初始价：
                </div>
                <div class="list-item-right">
                  <input type="number" min=0 v-auto-clear class="form-control" v-model="card2.initPrice" @change="countPer" placeholder="请输入股票初始价">
                </div>
              </div>
            </li>
            <li class="list-item">
              <div class="list-item-inner">
                <div class="list-item-left">
                  现价：
                </div>
                <div class="list-item-right">
                  <input type="number" min=0 v-auto-clear class="form-control" v-model="card2.currentPrice" @change="countPer" placeholder="请输入现价">
                </div>
              </div>
            </li>
            <li class="list-item">
              <div class="list-item-inner">
                <div class="list-item-left">
                  涨跌幅：
                </div>
                <div class="list-item-right">
                  <input type="text" disabled v-model="card2.per" class="form-control">
                </div>
              </div>
            </li>
        </ul>
        <ul class="list">
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                年化率(%)：
              </div>
              <div class="list-item-right">
                <input type="number" min=0 v-auto-clear class="form-control" v-model="card3.per" @change="countDayPer(0)" placeholder="请输入年化率">
              </div>
            </div>
          </li>
          <li class="list-item">
            <div class="list-item-inner">
              <div class="list-item-left">
                万几：
              </div>
              <div class="list-item-right">
                <input type="number" min=0 v-auto-clear class="form-control" v-model="card3.dayCount" @change="countDayPer(1)" placeholder="请输入万化率">
              </div>
            </div>
          </li>
        </ul>
      </form>
    </div>
  </div>
</template>

<style lang="scss">
@import '@/assets/scss/index.scss';
.counter {
  display: inline-block;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background-color: $theme-color;
  line-height: 24px;
  font-size: 26px;
  color: #fff;
  text-align: center;
  vertical-align: middle;
  margin-right: 8px;
}
</style>
<script>
  export default {
    data() {
      return {
        sellPrice: '',
        buyPrice: '',
        currentPrice: '',
        radio: 1,
        per: 5,
        card2: {
          initPrice: '',
          currentPrice: '',
          per: ''
        },
        card3: {
          per: '',
          dayCount: ''
        }
      }
    },
    directives: {
      autoClear: {
        update(el) {
          el.addEventListener('focus', () => {
            el.value = ''
          })
        }
      }
    },
    methods: {
      countCurrency(type) {
        this.per += type * this.radio
        this.per = +this.per.toFixed(2)
        if (this.per < 0) {
          this.per = 0
        } else if (this.per > 10) {
          this.per = 10
        }
        if (!this.currentPrice) {
          return
        } else if (this.currentPrice < 0) {
          this.currentPrice = Math.abs(this.currentPrice)
        }
        this.sellPrice = +(this.currentPrice * (1 + this.per / 100)).toFixed(2)
        this.buyPrice = +(this.currentPrice * (1 - this.per / 100)).toFixed(2)
      },
      countPer() {
        if (this.card2.initPrice && this.card2.currentPrice) {
          var result = (100 * (this.card2.currentPrice - this.card2.initPrice) / this.card2.initPrice).toFixed(2)
          if (this.card2.currentPrice - this.card2.initPrice > 0) {
            result = "+" + result + "%";
          } else if (this.card2.currentPrice - this.card2.initPrice == 0){
            result = 0;
          } else {
            result = "-" + result + "%";
          }
          this.card2.per = result
        }
      },
      countDayPer(type) {
        if(!type && this.card3.per) {
          this.card3.dayCount = (this.card3.per * 100 / 365).toFixed(2)
        } else if (this.card3.dayCount) {
          this.card3.per = (this.card3.dayCount * 365 / 100).toFixed(2)
        }
      }
    }
  }
</script>