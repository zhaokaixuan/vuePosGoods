<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' id="order-list">
        <el-tabs>
          <el-tab-pane label="点餐">
            <el-table :data="tableData" border style="width: 100%">
              <el-table-column show-overflow-tooltip prop="goodsName" label="商品"></el-table-column>
              <el-table-column prop="count" align="center" label="数量" width="60"></el-table-column>
              <el-table-column prop="price" align="center" label="金额" width="70">
                <template scope="scope">
                  {{ scope.row.count*scope.row.price }}
                </template>
              </el-table-column>
              <el-table-column label="操作" align="center" width="90">
                <template scope="scope">
                  <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                  <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                </template>
              </el-table-column>
            </el-table>
            <div class="total" v-show="totalCount">
              <div class="totalCount">
                <small>总数:</small>
                <span>{{ totalCount }}个</span>
              </div>
              <div class="totalMoney">
                <small>总额:</small>
                <span>￥{{ totalMoney }}元</span>
              </div>
            </div>
            <div class="list-btn-ground">
              <el-button type="warning">挂单</el-button>
              <el-button type="danger" @click="delAllGoods">删除</el-button>
              <el-button type="success" @click="checkOut">结账</el-button>
            </div>
          </el-tab-pane>
          <el-tab-pane label="挂单">
            挂单
          </el-tab-pane>
          <el-tab-pane label="外卖">
            外卖
          </el-tab-pane>
        </el-tabs>
      </el-col>
      <!--商品展示-->
      <el-col :span="17">
        <div class="often-goods">
          <div class="title">常用商品</div>
          <div class="often-goods-list">
            <ul>
              <li v-for="good in oftenGoods" @click="addOrderList(good)">
                <span class="o-name">{{ good.goodsName }}</span>
                <span class="o-price">￥{{ good.price }}元</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="goods-type">
          <el-tabs>
            <el-tab-pane label="汉堡">
              <ul class='cookList'>
                <li v-for="good in type0Goods" @click="addOrderList(good)">
                  <span class="foodImg">
                    <img :src="good.goodsImg" height="80px" width="100%">
                  </span>
                  <span class="foodName">{{ good.goodsName }}</span>
                  <span class="foodPrice">￥{{ good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="小食">
              <ul class='cookList'>
                <li v-for="good in type1Goods" @click="addOrderList(good)">
                  <span class="foodImg">
                    <img :src="good.goodsImg" height="80px" width="100%">
                  </span>
                  <span class="foodName">{{ good.goodsName }}</span>
                  <span class="foodPrice">￥{{ good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="饮料">
              <ul class='cookList'>
                <li v-for="good in type2Goods" @click="addOrderList(good)">
                  <span class="foodImg">
                    <img :src="good.goodsImg" height="80px" width="100%">
                  </span>
                  <span class="foodName">{{ good.goodsName }}</span>
                  <span class="foodPrice">￥{{ good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
            <el-tab-pane label="套餐">
              <ul class='cookList'>
                <li v-for="good in type3Goods" @click="addOrderList(good)">
                  <span class="foodImg">
                    <img :src="good.goodsImg" height="80px" width="100%">
                  </span>
                  <span class="foodName">{{ good.goodsName }}</span>
                  <span class="foodPrice">￥{{ good.price }}元</span>
                </li>
              </ul>
            </el-tab-pane>
  
          </el-tabs>
        </div>
      </el-col>
    </el-row>
  </div>
</template>
 
<script>
import axios from 'axios';
export default {
  name: 'Pos',
  data() {
    return {
      totalMoney: 0,
      totalCount: 0,
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      tableData: []
    }
  },
  created: function () {
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
      .then(reponse => {
        //console.log(reponse);
        this.oftenGoods = reponse.data;
      })
      .catch(error => {
        //console.log(error);
        alert('网络错误，不能访问');
      })
    //读取分类商品列表
    axios.get('http://jspang.com/DemoApi/typeGoods.php')
      .then(response => {
        //console.log(response);
        this.type0Goods = response.data[0];
        this.type1Goods = response.data[1];
        this.type2Goods = response.data[2];
        this.type3Goods = response.data[3];
      })
      .catch(error => {
        //console.log(error);
        alert('网络错误，不能访问');
      })
  },
  methods: {
    isHave: function (goods) {//判断商品是否存在
      var arr = this.tableData.filter(item => item.goodsId == goods.goodsId);
      return arr;
    },
    totoal:function(){
      this.totalCount = 0; //汇总数量清0
      this.totalMoney = 0;
      //进行数量和价格的汇总计算
      this.tableData.forEach((element) => {
        this.totalCount += element.count;
        this.totalMoney = this.totalMoney + (element.price * element.count);
      });
    },
    addOrderList: function (goods) {//添加商品
      var arr = this.isHave(goods);
      if (arr.length) {
        arr[0].count++;
      } else {
        //不存在就推入数组
        var newGoods = { goodsId: goods.goodsId, goodsName: goods.goodsName, price: goods.price, count: 1 };
        this.tableData.push(newGoods);
      }
      this.totoal();
    },
    delSingleGoods(goods){//当个商品删除减一
      if(--goods.count==0){
        this.tableData=this.tableData.filter(item=>item.goodsId!=goods.goodsId);
      }
      this.totoal();
    },
    checkOut(){
      if(this.totalCount){
        this.delAllGoods();
        this.$message({
          message:'结账成功,继续努力！！！',
          type:'success'
        });
      }
    },
    delAllGoods:function(){
      this.tableData=[];
      this.totalCount = 0; //汇总数量清0
      this.totalMoney = 0;
    }
  }
}
</script>
<style scoped>
.pos,
.el-row,
#order-list,
.el-tabs {
  height: 100%;
}

#order-list {
  background-color: #fff;
}

.list-btn-ground {
  margin: 10px auto;
  text-align: center;
}

.title {
  height: 21px;
  border-bottom: 1px solid #a8bed6;
  background-color: #F9FAFC;
  padding: 10px;
}

.often-goods-list ul {
  margin: 10px auto;
}

.often-goods-list ul li {
  list-style: none;
  float: left;
  border: 1px solid #E5E9F2;
  padding: 10px;
  margin: 0px 3px 10px 3px;
  width: 147px;
  text-align: center;
  cursor: pointer;
  background-color: #fff;
}

.o-name {
  float: left;
  color: #6e8299;
}

.o-price {
  float: right;
  color: #FF5722;
}

.goods-type {
  clear: both;
}

.cookList li {
  list-style: none;
  padding: 2px;
  margin: 0 15px 0 0px;
  cursor: pointer;
  width: 23%;
  border: 1px solid #E5E9F2;
  height: 80px;
  overflow: hidden;
  background-color: #fff;
  float: left;
  box-shadow: 0 2px 2px rgb(203, 208, 217);
}

.cookList li span {
  display: block;
  float: left;
}

.foodImg {
  width: 40%;
}

.foodName {
  font-size: 18px;
  height: 50%;
  line-height: 30px;
  width: 60%;
  color: #6e8299;
  text-align: center;
}

.foodPrice {
  font-size: 16px;
  height: 50%;
  line-height: 23px;
  width: 60%;
  color: #FF5722;
  text-align: center;
}

.total {
  height: 40px;
  line-height: 40px;
  background: #FFF8E1;
  border: 1px solid rgb(231, 235, 243);
  border-top: 0px solid rgb(231, 235, 243);
}

.totalCount {
  width: 50%;
  float: left;
  text-align: right;
}

.totalCount span {
  color: #6e8299;
  padding: 0 10px;
}

.totalMoney {
  width: 50%;
  float: left;
  text-align: center;
}

.totalMoney span {
  color: #FF5722;
  padding: 0 10px;
}
</style>