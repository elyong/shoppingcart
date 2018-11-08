<template>
  <div id="container">
    <div class="title">购物清单</div>
    <div class="goods">
      <table class='detail'>
        <thead class="table-title">
        <td>
          <label for="all">
            <input id="all" type="checkbox" @click="checkAll($event)"/> 全选
          </label>
        </td>
        <td><div class="table-head">商品</div></td>
        <td><div class="table-head">数量</div></td>
        <td><div class="table-head">单价</div></td>
        <td><div class="table-head"><span>金额</span></div></td>
        <td><span>操作</span></td>
        </thead>
        <tbody class="table-detail">
        <tr class="table-detail-row" v-for="item in goodsList" :key="item.index">
          <td>
            <label>
              <input class="checkItem" type="checkbox" :value="item.name" v-model="checkData"/>
            </label>
          </td>
          <td><div class="goods-detail">
            <img class="goods-img":src="item.img"/>
            {{item.name}}{{item.detail}}
          </div></td>
          <td>
            <div class="number">
              <span style="cursor: pointer;" @click="minus(item.name)">-</span>
                {{item.num}}
              <span style="cursor: pointer;" @click="add(item.name)">+</span>
            </div>
          </td>
          <td><div class="price">￥{{item.price}}</div></td>
          <td><div class="pertotal">￥{{item.price*item.num}}</div></td>
          <td @click="deleteItemGroup(item.name)"><span class="del">删除</span></td>
        </tr>
        </tbody>
      </table>
    </div>
    <div class="operation">
      <div class="delete-and-continue">
        <span class="delete-pick-all" @click="deleteItemGroups(checkData)">删除所选商品</span>
        <span class="continue">继续购物</span>
      </div>
      <div class="total-and-pay">
        <span class="total">{{checkData.length}}件商品总计（不含运费）：<span class="total-price">￥{{totalPrice}}元</span></span>
        <div class="pay">去结算</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data: function () {
    return {
      checkData:[],
      goodsList:[
        {index: 0, name: 'apple', img:'/img/goods.jpg',price: 14, num: 0},
        {index: 1, name: 'banana', img:'/img/goods.jpg',price: 8, num: 0},
        {index: 2, name: 'orange', img:'/img/goods.jpg',price: 3, num: 0},
        {index: 3, name: 'kk', img:'/img/goods.jpg',price: 3, num: 0},
        {index: 4, name: 'yyy', img:'/img/goods.jpg',price: 3, num: 0}
      ],
      totalPrice:0
    }
  },
  watch: {
    checkData: {
      handler(){
        if(this.checkData.length === this.goodsList.length&&this.checkData.length!=0&&this.goodsList.length!=0){
          document.querySelector('#all').checked = true;
        }else {
          document.querySelector('#all').checked = false;
        }
        this.getTotal()
      },
      deep: true
    }
  },
  methods: {
    checkAll(e) {
      if (e.target.checked) {
        this.goodsList.forEach((item, index) => {
          if (this.checkData.indexOf(item.name) == '-1') {
            this.checkData.push(item.name);
          }
        });
      } else {
        this.checkData = [];
      }
    },
    add:function(name){
      this.goodsList.forEach((item)=>{
        if(item.name===name){
          item.num++;
        }
      })
      this.getTotal()
    },
    minus:function(name){
      this.goodsList.forEach((item)=>{
        if(item.name===name&&item.num>0){
          item.num--;
        }
      })
      this.getTotal();
    },
    deleteItemGroup:function(name){
      this.goodsList.forEach((item,index)=> {
        if (item.name === name) {
          this.goodsList.splice(index, 1)
          this.checkData.splice(this.checkData.indexOf(name), 1)
        }
      })
      this.getTotal()
    },
    deleteItemGroups:function(names){
      this.goodsList.forEach((item,index)=>{
        names.forEach((name)=>{
          if(item.name===name){
            this.goodsList.splice(index,1)
            this.checkData.splice(this.checkData.indexOf(name),1)
            names=this.checkData
            if(names.length>0){
              this.deleteItemGroups(names)//暂时想到的是重新循环执行一次，避免数组变动影响splice方法执行的结果
              return;//减少this.getTotal()的开销
            }
          }
        })
      })
      this.getTotal()
    },
    getTotal:function(){
      this.totalPrice=0;
      let perTotalPrice;
      this.goodsList.forEach((item) => {
        this.checkData.forEach((name)=>{
          if(item.name===name){
            perTotalPrice=item.num*item.price;
            this.totalPrice=this.totalPrice+perTotalPrice;
          }
        })
      })
    }
  }
}

</script>

<style>
  body{
    font-size:13px;
  }
  #container{
    min-width:1024px;
    max-width:1440px;
    margin:0 auto;
    border-style:solid;
    border-color: #5868ff;
    border-width:2px 0 0 0;
  }
  .title{
    border:1px solid #D5D5D5;
    padding:10px;
    color:#5868ff;
  }
  .goods{
    border:1px solid #D5D5D5;
    padding:0 0 10px 0;
  }
  .detail{
    width:100%;
    border-collapse: collapse;
  }
  .detail .table-head{
    padding:8px 0;
    text-align:center;
  }
  .detail thead{
    border-top:0;
    border-right:0;
    border-bottom:1px;
    border-left:0;
    border-style:solid;
    border-color:#D5D5D5
  }
  .detail tbody{
    border-top:1px;
    border-right:0;
    border-bottom:0;
    border-left:0;
    border-color:#D5D5D5;
    border-style:solid;
  }
  .detail .goods-img{
    width:100px;
    height:100px;
  }
  .detail .number{
    display:flex;
    justify-content: space-around;
    margin:5px auto;
    width:50px;
    border-top:1px;
    border-right:1px;
    border-bottom:0;
    border-left:1px;
    border-color:#D5D5D5;
    border-style:solid;
  }
  .detail .price,.detail .pertotal{
    color: #ff4222;
    width:50px;
    text-align:center;
    margin:0 auto;
  }
  .detail .del{
    cursor:pointer;
  }
  .operation{
    border:1px solid #D5D5D5;
    height:50px;
  }
  .operation .delete-and-continue{
    display: inline-block;
  }
  .operation .delete-and-continue .delete-pick-all{
    cursor:pointer;
    line-height:50px;
  }
  .operation .delete-and-continue .delete-pick-all:before{
    display:inline-block;
    content:"";
    background:url("/img/bin.png") no-repeat;
    background-size:100% 100%;
    width:14px;
    height:14px;
    margin: 0 10px;
  }
  .operation .delete-and-continue .continue{
    cursor:pointer;
  }
  .operation .delete-and-continue .continue:before{
    display:inline-block;
    content:"";
    background:url("/img/cart.png") no-repeat;
    background-size:100% 100%;
    width:14px;
    height:14px;
    margin: 0 5px;
  }
  .operation .total-and-pay{
    display: inline-block;
    float:right;
  }
  .operation .total-and-pay .total{
    margin:0 5px;
    display: inline-block;
  }
  .operation .total-and-pay .total .total-price{
    display: inline-block;
    color: #ff3d19;
    font-size:20px;
  }
  .operation .total-and-pay .pay{
    display: inline-block;
    width:100px;
    height:50px;
    text-align: center;
    font-size:18px;
    line-height: 50px;
    color:white;
    background-color: #ff812b;
    cursor:pointer;
  }
  input[type="checkbox"]{
    width:15px;
    height:15px;
    display: inline-block;
    text-align: center;
    vertical-align: middle;
    line-height: 18px;
    position: relative;
  }
  input[type="checkbox"]::before{
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    background: #fff;
    width: 100%;
    height: 100%;
    border: 1px solid #d9d9d9
  }
  input[type="checkbox"]:checked::before{
    content: "\2713";
    background-color: #fff;
    position: absolute;
    top: 0;
    left: 0;
    width:100%;
    color:#FF9900;
    font-size: 15px;
    font-weight: bold;
  }
</style>
