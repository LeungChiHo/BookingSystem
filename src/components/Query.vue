<template>
  <section class="container"> <!-- 最外层容器 -->
    <el-menu theme="dark" :default-active="activeIndex" class="el-menu-demo" mode="horizontal">
      <el-menu-item index="1"><router-link to="/home">主页</router-link></el-menu-item>
    </el-menu>
    <div>
        <div id= "space"></div>
       
        <div>
          <div id="curOrder">
          <img id="menu_icon" src="../assets/food.png">
          当前订单
          <img id="menu_icon" src="../assets/food.png">
          </div>
        </div>
        <div id= "space"></div>
    </div>
    <div id="line_query"></div>
    <section class="content-container"><!-- 内容容器 -->
      <div id="container">
        <div id="tableInfo">
          <div id="ellipse_query">
            <img src="../assets/query.png">
          </div>
          <p id="tableTime">用餐时间：{{tableData.time}}</p>
          <p id="tablePhone">联系电话： {{tableData.phone}}</p>
        </div>
        <div id="table_type_query">
          <div id="ellipse_query">
            <img src="../assets/querychair.png">
          </div>
          <div id="tableMem">
            <p id="tableId">桌型：{{tableData.table}} 人桌</p>
          </div>
        </div>
        <div id="table_food_query">
          <div id="ellipse_query">
            <img src="../assets/food.png">
          </div>
          <div id="foodInfo">
            <div class="foodList">
              <el-table :data="tableData.foods">
                <el-table-column property="name" label="菜名" align="center" ></el-table-column>
                <el-table-column property="price" label="价格" align="center" ></el-table-column>
              </el-table>
              <div class="totalPrice">
                总价：{{total}}
              </div>
            </div>
          </div>
        </div>
      </div>
      <div id="line_query_2">
      </div>
      <div id="query_but">
        <el-button  @click="deleteOrder">删除订单</el-button>
        
      </div>
      <div id="line_query_3"></div>
    </section>
    <br/>
  </section>
</template>

<script>
import item from './item.vue'
export default {
  components: {
    item
  },
  data () {
    return {
      total: 0,
      tableData: [],
      loading: false
    }
  },
  methods: {
    getAll: function () {
      this.loading = true;
      let phone = localStorage.getItem('user');
      this.$http.post("/api/getFood",{phone}).then(
        function(response) {
          this.loading = false;
          this.tableData = response.body;
          this.computeTotal();
        },
        function() {
          this.loading = false;
          console.log("error");
        }
      );
    },
    computeTotal: function () {
       for (let index = 0; index <  this.tableData.foods.length; index++) {
        this.total = this.tableData.foods[index].price + this.total;
      }
    },
    deleteOrder: function() {
      var that = this;
      var myphone = localStorage.getItem('user');
        
      this.$confirm("删除此订单, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.$http.put(`/api/deleteMyOrder/${myphone}`).then(
            function(response) {
              if (response.ok) {
                this.$message({
                  type: "success",
                  message: "删除订单成功!"
                });
                this.total = 0;
                that.getAll();
              } else {
                this.$message({
                  type: "error",
                  message: "删除订单失败!"
                });
              }
            }
          );
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    }
  },

    //页面初始化进来查询数据
  mounted: function() {
    this.getAll();
  }
}
</script>

<style>
  #space {
    float: right;
    width: 80%;
    /* border: 2px solid rgb(110, 86, 7); */
  }
  *{
    margin: 0;
    padding: 0;
  }
  #menu_icon {
    height: 50px;
    width: 50px;
  }
  #line_query_3 {
    margin-top:-15px;
    margin-left: 1380px;
    width:30%;
    height:3px;
    background:rgb(051, 069, 091);
  }
  #line_query_2 {
    margin-top:300px;
    margin-left: 200px;
    width:30%;
    height:3px;
    background:rgb(051, 069, 091);
  }
  #line_query {
    margin-top:80px;
    margin-left: 200px;
    width:80%;
    height:2px;
    background:rgb(051, 069, 091);
  }
  #query_but {
    margin-top: -20px;
    margin-left: 1050px;
    background-color:rgb(051, 069, 091);
    width: 200px;
    height: 35px;
    box-shadow: #666 0px 0px 10px;
  }
  #ellipse_query {
    box-shadow: #666 0px 0px 10px;
    width: 150px;
    height: 150px;
    margin-top: 30px;
    margin-left: 150px;
    border: 4px solid rgb(051, 069, 091);
    border-radius: 50%;
  }

  #ellipse_query img {
    width: 100%;
    height:100%;
  }

  #container {
    height: 400px;
    /* border: 1px solid blue; */
  }
  #menu {
    margin-left:-35px;
    height: 400px;
    width: 20%;
  }
  #curOrder {
    margin-left: 450px;
    height: 40px;
    width: 60%;
    color: rgb(051, 069, 091);
    font-size: 40px;
    font-family: Roboto;
    margin-top: 30px;
  }
  #table_type_query {
    background-image: url('../assets/bg3.jpeg');
    background-size:cover;
    margin-top: 40px;
    margin-left: 100px;
    background-color: white;
    float: left;
    height: 600px;
    width: 20%;
    box-shadow: #666 0px 0px 10px;
    border:  3px solid rgb(051, 069, 091);
  }
  #tableInfo {
    background-image: url('../assets/bg1.png');
    margin-top: 40px;
    margin-left: 350px;
    background-color: white;
    float: left;
    height: 600px;
    width: 20%;
    box-shadow: #666 0px 0px 10px;
    border:  3px solid rgb(051, 069, 091);
  }
  #table_food_query {
    background-image: url('../assets/bg2.jpeg');
    margin-top: 40px;
    margin-left: 100px;
    background-color: white;
    float: left;
    height: 600px;
    width: 20%;
    box-shadow: #666 0px 0px 10px;
    border:  3px solid rgb(051, 069, 091);
  }
  #deleteIcon{
    width: 30px;
  }
  #tableMem {
    text-shadow: #666 0px 0px 10px;
    color: rgb(051, 069, 091);
    font-size: 35px;
    font-family: Roboto;
    margin-top: 50px;
    margin-left: -10px;
    text-decoration: underline;
  }
  #tableId{
    text-shadow: #666 0px 0px 10px;
    color: rgb(051, 069, 091);
    font-size: 35px;
    font-family: Roboto;
    margin-top: 100px;
    margin-left: -10px;
    text-decoration: underline;
  }
  #tableTime {
    text-shadow: #666 0px 0px 10px;
    color: rgb(051, 069, 091);
    font-size: 35px;
    font-family: Roboto;
    margin-top: 50px;
    margin-left: -10px;
    text-decoration: underline;
  }
  #tablePhone {
    color: rgb(051, 069, 091);
    font-size: 35px;
    font-family: Roboto;
    margin-top: 50px;
    margin-left: 20px;
    text-decoration: underline;
  }
  #foodInfo {
    text-shadow: #666 0px 0px 10px;
    text-decoration: underline;
    float:left;
    margin-top: 40px;
    margin-left: -15px;
    height: 500px;
    width: 100%;
    color:rgb(051, 069, 091);
    font-family: Roboto;
  }
  .foodList {
    box-shadow: #666 0px 0px 10px;
    font-size: 25px;
    margin-left: 85px;
    width: 70%;
    height: 300px;
    /*box-shadow: #666 0px 0px 5px;*/
    border:  3px solid rgb(051, 069, 091);
  }

</style>