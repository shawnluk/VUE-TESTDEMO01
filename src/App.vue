<template>
  <div class="app-container">
    <!-- <h1>App 根组件</h1> -->
    <Header></Header>
    <Goods
    v-for="item in GoodsList" 
    :key="item.id"
    :goodsId="item.id"
    :goodsTitle="item.goods_name"
    :goodsPic="item.goods_img"
    :goodsPrice="item.goods_price"
    :goodsState="item.goods_state"
    :goodsCount="item.goods_count"
    @stateChange="getStateChange"
    >
    </Goods>

    <Footer 
    @SelectFull="getSelectFullState"
    :isFull="isFull"
    :PriceAmt="PriceAmt"
    :numAmt="numAmt"
    ></Footer>
  </div>
</template>

<script>
import axios from 'axios'
import bus from '@/components/eventBus.js'

import Header from '@/components/Header/Header.vue'
import Goods  from '@/components/Goods/Goods.vue'
import Footer from '@/components/Footer/Footer.vue'


export default {
  computed:{
    isFull(){
      return this.GoodsList.every(item=>item.goods_state === true)
    },
    PriceAmt(){
      return this.GoodsList.filter(item=>item.goods_state).reduce((val,item)=> val += item.goods_price * item.goods_count,0)
    },
    numAmt(){
      return this.GoodsList.filter(item=>item.goods_state).reduce((val,item)=>val += item.goods_count,0)
    }
  },
  created(){
    // console.log('created函数');
    this.getGoodsList()
    bus.$on('share',(val)=> {
      // console.log(val);
      this.GoodsList.some(item=>{
        if(item.id === val.id){
          item.goods_count = val.value    
        return true
        }
      })
    })
  },

  methods:{
    async getGoodsList(){
      const {data:res} = await axios.get('https://www.escook.cn/api/cart')
      // console.log(res);
      this.GoodsList = res.list
    },
    getStateChange(e){
      this.GoodsList.some((item,index)=>{
        if(item.id === e.id){
          // console.log([index,item.id,e.value]);
          this.GoodsList[index].goods_state = e.value
          return true
        }
      })
      
    },
    getSelectFullState(e){
      // console.log(e);
      this.GoodsList.forEach(item=>item.goods_state = e)
    },
  },
  data(){
    return {
      GoodsList:[]
    }
  },
  components:{
    Header,
    Goods,
    Footer
  }
}

</script>

<style lang="less" scoped>
.app-container {
  padding-top: 45px;
  padding-bottom: 50px;
}
</style>
