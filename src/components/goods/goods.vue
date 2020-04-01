<template>
  <div class="good">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul style="margin-top: -5px">
        <li v-for="(item, index) in goods" class="menu-item border-1px" :class="{'current':currentIndex === index}"
            @click="selectMenu(index, $event)">
          <span class="text">
            <span v-show="item.type>0" class=" icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodWrapper">
      <ul style="list-style-type: none">
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul style="margin-left: -38px">
            <li v-for="food in item.foods" class="food-item" @click="selectFood(food, $event)">
              <div class="icon">
                <img :src="food.icon" alt="" width="57">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <!--<p class="desc">{{food.description}}</p>-->
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span><span class="count">好评{{food.rating}}</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span class="old"
                                                                v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartControl-wrapper">
                  <cartControl :food="food" @increment="incrementTotal"></cartControl>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <div>
      <!--底部购物车显示组件-->
      <shopCart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" ref="shopCart"></shopCart>
      <!--食物详情页组件-->
      <food :food="selectedFood" ref="food"></food>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopCart from '../shopcart/shopCart.vue';
  import cartControl from '../cartControl/cartControl.vue';
  import food from '../food/food.vue';
  import data from '../../common/json/data';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrolly: 0,
        selectedFood: {}
      };
    },
    created() {
      this.goods = data.goods;
      this.$nextTick(() => {
        this._initScroll();
        this._calculateHeight();
      });
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    computed: {
      //当前食品列表所属的菜单的索引
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrolly >= height && this.scrolly < height2)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    methods: {
      _initScroll() {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodScroll = new BScroll(this.$refs.foodWrapper, {
          probeType: 3,
          click: true
        });
        this.foodScroll.on('scroll', (pos) => {
          this.scrolly = Math.abs(Math.round(pos.y));
          // console.log(pos.y)
        });
      },
      //将左侧菜单栏每一项的高度放进this.height数组
      _calculateHeight() {
        let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      },
      //点击菜单中的相应按钮食物列表滚动到响应位置
      selectMenu(index, event) {
        if (!event._constructed) {
          // 去掉自带click事件的点击
          return;
        }
        let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodScroll.scrollToElement(el, 300);
      },
      //点击食品，显示食品详情页
      selectFood(food, event) {
        if (!event._constructed) {
          // 去掉自带click事件的点击
          return;
        }
        this.selectedFood = food;
        //this.$refs调用子组件的方法
        this.$refs.food.show();
      },
      //这个方法在shopCart组件中 [this.$refs.子组件.方法() （父组件调用子组件中的方法）]
      incrementTotal(target) {
        this.$refs.shopCart.drop(target)
      }
    },
    components: {
      shopCart,
      cartControl,
      food
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "goods.styl";
</style>
