<template>
	<div class="pos">
		<el-row>
			<el-col :span="7" class="order-list" id="orderList">
				<el-tabs class="order-tabs">
					<el-tab-pane label="结账">
						<el-table border :data="tableData">
							<el-table-column prop="goodsName" label="商品名称" align="center"></el-table-column>
							<el-table-column prop="price" label="价格" width="70" align="center"></el-table-column>
							<el-table-column prop="count" label="数量" width="70" align="center"></el-table-column>
							<el-table-column label="操作" align="center" fixed="right">
								<template slot-scope="scope">
									<el-button type="text" size="small" @click="deleteSingGoods(scope.row)">删除</el-button>
									<el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
								</template>
							</el-table-column>
						</el-table>
						<div class="total-box">
							<small>数量：</small>{{totalCount}} <small>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;总价：</small>￥{{totalPrice}}元
						</div>
						<div class="order-btn">
							<el-button type="warning">挂单</el-button>
							<el-button type="danger" @click="deleteAllGoods()">删除</el-button>
							<el-button type="success" @click="checkout()">结账</el-button>
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
			<el-col :span="17">
				<div class="goods-box">
					<div class="main-title">常用商品</div>
					<div class="main-goods">
						<ul>
							<li v-for="goods in oftenGoods" @click="addOrderList(goods)">
								<span>{{goods.goodsName}}</span>
								<span class="goods-price">￥{{goods.price}}</span>
							</li>
						</ul>
					</div>
				</div>
				<div class="goods-type">
					<el-tabs>
						<el-tab-pane label="汉堡">
							<div>
								<ul class="cook-list">
									<li v-for="goods in type0Goods" @click="addOrderList(goods)">
										<span class="cook-img"><img :src="goods.goodsImg"></span>
										<span class="cook-name">{{goods.goodsName}}</span>
										<span class="cook-price">￥{{goods.price}}元</span>
									</li>
								</ul>
							</div>
						</el-tab-pane>
						<el-tab-pane label="小食">
							<div>
								<ul class="cook-list">
									<li v-for="goods in type1Goods">
										<span class="cook-img"><img :src="goods.goodsImg"></span>
										<span class="cook-name">{{goods.goodsName}}</span>
										<span class="cook-price">￥{{goods.price}}元</span>
									</li>
								</ul>
							</div>
						</el-tab-pane>
						<el-tab-pane label="饮料">
							<div>
								<ul class="cook-list">
									<li v-for="goods in type2Goods">
										<span class="cook-img"><img :src="goods.goodsImg"></span>
										<span class="cook-name">{{goods.goodsName}}</span>
										<span class="cook-price">￥{{goods.price}}元</span>
									</li>
								</ul>
							</div>
						</el-tab-pane>
						<el-tab-pane label="套餐">
							<div>
								<ul class="cook-list">
									<li v-for="goods in type3Goods">
										<span class="cook-img"><img :src="goods.goodsImg"></span>
										<span class="cook-name">{{goods.goodsName}}</span>
										<span class="cook-price">￥{{goods.price}}元</span>
									</li>
								</ul>
							</div>
						</el-tab-pane>
					</el-tabs>
				</div>
			</el-col>
		</el-row>
	</div>
</template>
<script type="text/javascript">
	import axios from 'axios';
	export default{
		name: 'pos',
		data(){
			return{
				tableData:[],
				oftenGoods: [],
				type0Goods:[],
				type1Goods:[],
				type2Goods:[],
				type3Goods:[],
				totalCount: 0,
				totalPrice: 0
			}
		},
		created: function(){
			axios.get("https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods")
			.then(response => {
				this.oftenGoods = response.data;
				this.type0Goods = response.data;
				this.type1Goods = response.data;
				this.type2Goods = response.data;
				this.type3Goods = response.data;
			})
			.catch(error => {
				alert("网络错误，不能访问");
			})
		},
		mounted:function(){
			var orderHeight = document.body.clientHeight;
			document.getElementById("orderList").style.height = orderHeight + "px";
		},
		methods:{
			addOrderList(goods){
				// 商品是否已经存在于订单列表中
				let isHave = false;
				for(let i = 0; i < this.tableData.length; i++){
					if(goods.goodsId == this.tableData[i].goodsId){
						isHave = true;
					}	
				}
				// 根据判断的值编写业务逻辑
				if(isHave){
					let arr = this.tableData.filter(o => o.goodsId == goods.goodsId);
					arr[0].count++;
				}else{
					var addGoods = {
						goodsId: goods.goodsId,
						goodsName: goods.goodsName,
						price: goods.price,
						count: 1
					};
					this.tableData.push(addGoods);
				}
				this.getTotal();
				
			},
			// 删除单个商品
			deleteSingGoods(goods){
				this.tableData = this.tableData.filter( o => o.goodsId != goods.goodsId);
				this.getTotal();
			},
			// 全部删除商品
			deleteAllGoods(){
				this.tableData = [];
				this.totalCount = 0;
				this.totalPrice = 0;
			},
			// 计算总数量和总价钱
			getTotal(){
				this.totalCount = 0;
				this.totalPrice = 0;
				if(this.tableData){
					this.tableData.forEach((element) => {
						this.totalCount += element.count;
						this.totalPrice += (element.count * element.price);
					})
				}
			},
			// 结账
			checkout(){
				if(this.totalCount != 0){
					this.totalCount = 0;
					this.totalPrice = 0;
					this.tableData = [];
					this.$message.success("恭喜你，结账成功！");
				}else{
					this.$message({
						message: "亲~~，不能空结的哦~",
						type: "error"
					})
				}
			}
		}
	}
</script>
<style scoped>
	.order-list{
		background-color: #f9fafc;
		border-right: 1px solid #c0ccda;
	}
	.order-tabs{
		padding: 0 20px;
	}
	.order-btn{
		margin-top: 20px;
	}
	.main-title{
		height: 20px;
		border: 1px solid #d3dce6;
		background-color: #f9fafc;
		padding: 10px;
		text-align: left;
	}
	.main-goods ul li{
		float: left;
		border: 1px solid #1d8ce0;
		padding: 5px 10px;
		margin: 10px;
		background-color: #ffffff;
		border-radius: 5px;
		cursor: pointer;
	}
	.goods-price{
		color: #58b7ff;
	}
	.goods-type{
		clear: both;
		padding: 20px;
	}
	.cook-list li{
		float: left;
		width: 23%;
		border: 1px solid #e5e9f2;
		height: auto;
		overflow: hidden;
		background-color: #ffffff;
		padding: 2px;
		margin: 2px;
		cursor: pointer;
	}
	.cook-list li span{
		display: block;
		float: left;
	}
	.cook-img{
		width: 40%;
	}
	.cook-img img{
		width: 100%;
	}
	.cook-name{
		font-size: 16px;
		padding-left: 10px;
		color: brown;
		margin-top: 10px;
	}
	.cook-price{
		font-size: 16px;
		padding-left: 10px;
		padding-top: 10px;
	}
	.total-box{
		padding-top: 10px;
	}
</style>