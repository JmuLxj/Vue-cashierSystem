<template>
  <div class="pos">
    <el-row>
      <el-col :span='7' class="pos_order" id="order_list">
        <el-tabs style="padding-left:10px;">
            <el-tab-pane label="点餐">
              <el-table :data="tableData" border style="width:100%;">
                <el-table-column prop="goodsName" label="商品名称"> </el-table-column>
                <el-table-column prop="count" label="数量"> </el-table-column>
                <el-table-column prop="price" label="金额"> </el-table-column>
                <el-table-column label="操作" width="100" fixed='right'>
                  <template slot-scope='scope'>
                      <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                      <el-button type="text" size="small" @click="delGoods(scope.row)">删除</el-button>
                  </template>
                </el-table-column>
              </el-table>
              <div class="total">
                <small>数量:</small>{{ totalCount }}<small>金额:</small>{{ totalMoney }}
              </div>
              <div class="pos_btn">
                <el-button type="danger" @click="delAllGoods()">删除</el-button>
                <el-button type="success" @click="checkOut()">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单">
              暂无数据
            </el-tab-pane>
            <el-tab-pane label="外卖">
              暂无数据~
            </el-tab-pane>
        </el-tabs>
      </el-col>
        <el-col :span="17">
          <div class="often_goodsList">
            <div class="often_title">常用商品</div>
              <ul>
                <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                  <span>{{ goods.goodsName }}</span>
                  <span class="often_price">￥{{ goods.price }}元</span>
                </li>
              </ul>
          </div>
          <div class="goodsType">
            <el-tabs style="padding-left:10px;">
              <el-tab-pane label="汉堡" >

                <div>
                  <ul class='cookList'>
                    <li v-for="goods in type0Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>

               <el-tab-pane label="零食">
                <ul class='cookList'>
                    <li v-for="goods in type1Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
              </el-tab-pane>

               <el-tab-pane label="饮料">
                <ul class='cookList'>
                    <li v-for="goods in type2Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
              </el-tab-pane>

               <el-tab-pane label="套餐">
                <ul class='cookList'>
                    <li v-for="goods in type3Goods"  @click="addOrderList(goods)">
                      <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                      <span class="foodName">{{goods.goodsName}}</span>
                      <span class="foodPrice">￥{{goods.price}}元</span>
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
import axios from "axios";
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [],
      type0Goods: [],
      type1Goods: [],
      type2Goods: [],
      type3Goods: [],
      totalMoney: 0,
      totalCount: 0
    };
  },
  //  进行高度的计算
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    document.getElementById("order_list").style.height = orderHeight + "px";
  },
  // 创建时调用接口获取数据
  created: function() {
    axios
      .get("http://jspang.com/DemoApi/typeGoods.php")
      .then(data => {
        console.log(data);
        this.type0Goods = data.data[0];
        this.type1Goods = data.data[1];
        this.type2Goods = data.data[2];
        this.type3Goods = data.data[3];
      })
      .catch(error => {
        console.log(error, "调用接口失败");
      });
    axios
      .get("http://jspang.com/DemoApi/oftenGoods.php")
      .then(data => {
        console.log(data);
        this.oftenGoods = data.data;
      })
      .catch(errpr => {
        console.log(error, "失败");
      });
  },
  // 方法判断列表状态
  methods: {
    // 添加和计算商品价格
    addOrderList(goods) {
      // 商品是否已经存在于订单列表中
      let isHave = false;
      for (let i = 0; i < this.tableData.length; i++) {
        if (this.tableData[i].goodsId == goods.goodsId) {
          isHave = true;
        }
      }
      if (isHave) {
        // 改变列表中商品数量
        let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
        arr[0].count++;
      } else {
        let newGoods = {
          goodsId: goods.goodsId,
          goodsName: goods.goodsName,
          price: goods.price,
          count: 1
        };
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    // 删除单个商品
    delGoods(goods) {
      console.log(goods.goodsId, "删除了" + goods.goodsId);
      this.tableData = this.tableData.filter(o => o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    // 全部删除商品
    delAllGoods() {
      this.tableData = [];
      this.totalMoney = 0;
      this.totalCount = 0;
    },
    // 数量和价格的汇总计算
    getAllMoney() {
      this.totalMoney = 0;
      this.totalCount = 0;
      if (this.tableData) {
        this.tableData.forEach(element => {
          this.totalCount += element.count;
          this.totalMoney = this.totalMoney + element.price * element.count;
        });
      }
    },
    // 模拟结账
    checkOut() {
      if (this.totalCount != 0) {
        this.tableData = [];
        this.totalMoney = 0;
        this.totalCount = 0;
        this.$message({
          message: "结账成功,辛苦啦~",
          type: "success"
        });
      } else {
        this.$message.error("店员小哥哥、小姐姐，没有东西，怎么结账呀~");
      }
    }
  }
};
</script>

<style scoped>
.pos_order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.pos_btn {
  margin-top: 20px;
}
.often_title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often_goodsList ul li {
  list-style: none;
  float: left;
  border: 1px solid #d3dce6;
  padding: 10px;
  margin: 10px;
  background-color: #ffffff;
}
.often_price {
  color: #63b8ff;
}
.goodsType {
  clear: both;
  height: auto;
  overflow: hidden;
  border-top: 1px solid #d3dce6;
}

.cookList li {
  list-style: none;
  width: 23%;
  border: 1px solid #e5e9f2;
  height: auot;
  overflow: hidden;
  background-color: #fff;
  padding: 2px;
  float: left;
  margin: 2px;
  cursor: pointer;
}
.cookList li span {
  display: block;
  float: left;
}
.foodImg {
  width: 40%;
}
.foodName {
  font-size: 16px;
  padding-left: 10px;
  color: brown;
}
.foodPrice {
  font-size: 16px;
  padding-left: 10px;
  padding-top: 10px;
}
.total {
  background: #ffffff;
  padding: 10px;
  border-bottom: 1px solid #d3dce6;
}
.total small {
  padding: 0 10px;
  text-align: center;
}
</style>
