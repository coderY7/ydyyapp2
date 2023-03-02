<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
			</view>
			<view class="center">配调单收货</view>
			<view class="right"></view>
		</view>
		<view class="box-content">
			<view class="form-card">
				<view class="view-flex">
					<uni-datetime-picker v-model="uFormModel.range" type="daterange" :clear-icon="false" @change="dateChange"/>
					<text class="inp-right-text"></text>
				</view>
				<view class="view-flex" @tap="searchBm">
					<text class="form-left-text">部门编号:</text>
					<u-input placeholder="请选择部门编号" v-model="uFormModel.bmbh" readonly>
						<template slot="suffix">
							<view v-if="uFormModel.bmbh!=''" @tap.stop="clearAlone('bmbh')">
								<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
							</view>
						</template>
					</u-input>
					<text class="inp-right-text"></text>
				</view>
				<view class="view-flex" @tap="searchFd('chfd',false)">
					<text class="form-left-text">出货门店:</text>
					<u-input placeholder="请选择出货门店" v-model="uFormModel.chfd" readonly>
						<template slot="suffix">
							<view v-if="uFormModel.chfd!=''" @tap.stop="clearAlone('chfd')">
								<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
							</view>
						</template>
					</u-input>
					<text class="inp-right-text"></text>
				</view>
				<view class="view-flex" @tap="searchFd('shfd',false)">
					<text class="form-left-text">收货门店:</text>
					<u-input placeholder="请选择收货门店" v-model="uFormModel.shfd" readonly>
						<template slot="suffix">
							<view v-if="uFormModel.shfd!=''" @tap.stop="clearAlone('shfd')">
								<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
							</view>
						</template>
					</u-input>
					<text class="inp-right-text"></text>
				</view>
				<view class="view-flex">
					<text class="form-left-text">配调单号:</text>
					<u-input placeholder="请输入配调单号" v-model="uFormModel.pddh">
						<template slot="suffix">
							<view v-if="uFormModel.pddh!=''" @tap.stop="clearAlone('pddh')">
								<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
							</view>
						</template>
					</u-input>
					<text class="inp-right-text"></text>
				</view>
				<view class="view-flex">
					<text class="form-left-text">商品条码:</text>
					<u-input placeholder="请输入商品条码" v-model="uFormModel.spsmm">
						<template slot="suffix">
							<view v-if="uFormModel.spsmm!=''" @tap.stop="clearAlone('spsmm')">
								<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
							</view>
						</template>
					</u-input>
					<uni-icons custom-prefix="iconfont" type="icon-shouye" size="20" color="#606060" @tap="scan()"></uni-icons>
				</view>
				<view class="view-flex">
					<text class="form-left-text">单据状态:</text>
					<u-radio-group v-model="uFormModel.ztbz" placement="row">
						<u-radio key="T" label="待收货" name="T"></u-radio>
						<u-radio key="O" label="已收货" name="O"></u-radio>
					</u-radio-group>
				</view>
				
				<u-button type="primary" class="my-primary-button" text="查询" throttleTime="1000" @tap="search"></u-button>
			</view>
			
			<!-- 查询后的 内容 -->
			<view class="foldGroup">
				<view class="fold-title" v-for="(v,i) in tableData" @tap="tolook(v)">
					<view class="fold-title-t fold-title-flex-start">
						<text>{{v.配调单编号}}</text>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">收货分店:</text>
							<text class="right-con">{{v.收货分店}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">出货分店:</text>
							<text class="right-con">{{v.出货分店}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">出货仓库:</text>
							<text class="right-con">{{v.出货仓库}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">配调数量:</text>
							<text class="right-con">{{v.配调数量}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">配调金额:</text>
							<text class="right-con">{{v.配调金额}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">零售金额:</text>
							<text class="right-con">{{v.零售金额}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">成本金额:</text>
							<text class="right-con">{{v.成本金额}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">收货状态:</text>
							<text class="right-con" style="color:red;" v-if="v.收货状态=='待收货'">{{v.收货状态}}</text>
							<text class="right-con" style="color:green;" v-else>{{v.收货状态}}</text>
						</view>
					</view>
				</view>
			</view>
			
			<!-- 弹窗。。。。。。。。。。 -->
			<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
				<scroll-view style="max-height: 60vh;" scroll-y="true">
					<view v-if="ifDrawer=='bmbh'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange(ifDrawer,v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='chfd'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange(ifDrawer,v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='shfd'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange(ifDrawer,v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
				</scroll-view>
				<view class="closebtn" @tap="popupShow=false">取消</view>
			</u-popup>
			<u-toast ref="uToast"></u-toast>
			
			
		</view>
	</view>
</template>

<script>
	import dayjs from 'dayjs';
	import {
		Queryall,
		Basic,
		GetlistF,
	} from "@/network/api.js";
	export default {
		data() {
			return {
				uFormModel:{
					range: ["2022-01-01",dayjs().format('YYYY-MM-DD')],
					pddh: "",
					spsmm: "",
					bmbh: "",
					chfd: "",
					shfd: "",
					ztbz: "",
				},
				ifDrawer:"",
				selectData:[],
				tableData:[],
				popupShow:false
			}
		},
		onLoad() {
			
		},
		onReady() {
			// #ifdef APP-PLUS
			plus.navigator.setStatusBarStyle("light");
			// #endif
		},
		onShow() {
			// #ifdef APP-PLUS
			plus.navigator.setStatusBarStyle("light");
			// #endif
		},
		methods: {
			dateChange(event){
				this.uFormModel.range=event
			},
			// 搜索部门
			searchBm(){
				this.ifDrawer = "bmbh"
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"CompanyID": uni.getStorageSync("companyid"),
					"fdbh": uni.getStorageSync("fdbh"),
					"level": "1",
					"ParentsID": ""
				}
				Queryall(dataes).then((res) => {
					// console.log("queryBm res",res)
					if (res.error_code == 0) {
						this.selectData = []
						this.popupShow = true
						for (var i in res.nodes) {
							this.selectData.push({
								"id": res.nodes[i].id,
								"name": res.nodes[i].title
							})
						}
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.message
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			searchFd(type,bol){
				this.ifDrawer = type
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"dtype": "FDINFO",
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					console.log("分店 基本信息basic res", res)
					if (res.error_code == 0) {
						if (bol) { //自动填充
							this.uFormModel[type] = `${uni.getStorageSync("fdbh")}-${uni.getStorageSync("fdmc")}`
						} else {
							this.selectData = []
							this.popupShow = true
							for (var i in res.data) {
								this.selectData.push({
									"id": res.data[i].fdbh,
									"name": res.data[i].fdmc
								})
							}
						}
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.message
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			
			// 扫码 搜索商品
			scan() {
				uni.scanCode({
					success: (res) => {
						console.log('扫码内容', res)
						this.uFormModel.spsmm=res.result
					},
					fail: (err) => {
						this.$refs.uToast.show({
							type:"error",
							message: "识别失败"
						})
					}
				});
			}, 
			search(){
				this.tableData = []
				let tempBmbh=''
				if(this.uFormModel.bmbh){
					tempBmbh='\"'+this.uFormModel.bmbh.split('-')[0]+'\"'
				}
				let str =
					`'PHQRD','${this.uFormModel.range[0]}','${this.uFormModel.range[1]}','${tempBmbh}','${this.uFormModel.chfd.split("-")[0]}','${this.uFormModel.shfd.split("-")[0]}','${this.uFormModel.pddh}','${this.uFormModel.spsmm}','${this.uFormModel.ztbz}'`
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djtype": "PHQRD",
					"exeStr": str,
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
				}
				console.log("直配单收货 dataes",dataes)
				GetlistF(dataes).then((res) => {
					console.log("直配单收货 res", res)
					if (res.error_code == 0) {
						this.tableData = res.data
						this.$refs.uToast.show({
							type:"success",
							message: "查询成功"
						})
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.message
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			// 编辑单据
			tolook(event){
				// console.log("tolook event",event)
				uni.navigateTo({
					url: `/pages/function/component/pddsh/detail?dh=${event.配调单编号}`
				});
			},
			radioChange(name,event){
				this.uFormModel[name]=event
				this.popupShow=false
			},
			// 清除
			clearAlone(name) {
				this.uFormModel[name] = ""
				this.$forceUpdate()
			},
			
			
		},
		computed: {
			skin() {
				return this.$store.state.skin;
			}
		},
	}
</script>

<style lang="scss" scoped>
	.box{
		.status-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}
		.nav-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}
		
		.box-content {
			padding-top: calc(var(--status-bar-height) + 52px);
			
			.form-card{
				padding: 18px 10px;
				background-color: #fff;
				border-radius: 10px;
				box-sizing: border-box;
				box-shadow: 0px 1px 5px 3px #e8e8e8;
				position: relative;
				.view-flex{
					width: 100%;
					display: flex;
					justify-content: space-between;
					align-items: center;
				}
				/deep/.uni-date{
					width: 100%;
					.uni-date__x-input {
						height: 36px;
					}
					.uni-date-editor--x .uni-date__icon-clear {
						top: 6px;
						border: 2px solid transparent;
					}
					.uni-date-editor--x__disabled>.uni-date-x{
						background-color: #F5F7FA;
					}
				}
				/deep/.u-input__content__subfix-icon{
					flex-direction: row;
				}
				.inp-right-text{
					display: inline-block;
					width: 19px;
				}
				.form-left-text{
					display: block;
					width: 76px;
				}
			}
			.form-card>*{
				margin-bottom: 18px;
			}
			.form-card>*:last-child{
				margin-bottom: 0px;
			}
			
			.foldGroup{
				margin-top: 10px;
				.fold-title>*{
					margin-bottom: 8px;
				}
				.fold-title>*:last-child{
					margin-bottom: 0px;
				}
				.fold-title{
					margin-bottom: 10px;
					border-radius: 10px;
					box-sizing: border-box;
					padding: 10px;
					box-shadow: 0px 1px 5px 3px #e8e8e8;
					background-color: #fff;
					.fold-title-t{
						font-size: 17px;
						color: #1c1c28;
						font-weight: 600;
						margin-bottom: 4px;
						padding-bottom: 4px;
						border-bottom: 1px solid #ccc;
					}
					.fold-title-con{
						color: #999;
						font-weight: 400;
					}
					.fold-title-flex-start{
						display: flex;
						justify-content: flex-start;
						align-items: center;
					}
					.multiples {
						width: 100%;
						display: flex;
						justify-content: space-between;
						align-items: center;
						.multiple-con {
							width: 50%;
						}
					}
					.show-dots{
						overflow: hidden;
						text-overflow: ellipsis;
						display: -webkit-box;
						-webkit-line-clamp: 2;
						-webkit-box-orient: vertical;
					}
					.left-con{
						color: #999;
						font-weight: 400;
						margin-right: 4px;
					}
					.right-con{
						color: #1c1c28;
					}
					.cu1{
						color: #3a3a3a;
					}
					.cu-price{
						color: #358CC9;
					}
				}
			}
		}
		
		/deep/.u-popup {
			border-radius: 30px;
		
			.u-popup__content {
				padding-left: 15px;
				padding-right: 15px;
				padding-bottom: 10px;
				border-radius: 20px;
			}
		
			.uni-scroll-view-content {
				box-sizing: border-box;
				padding: 10px 0;
		
				.list {
					padding: 10px;
					border-bottom: 1px solid #E6E6E6;
				}
		
				.list>uni-text:first-child {
					color: #4A4A4A;
				}
		
				.list>uni-text:last-child {
					color: #A9A9A9;
				}
		
				.shop {
					.list {
						display: flex;
						padding: 10px 0;
		
						.shop-l {
							width: 24px;
							color: #060606;
							font-weight: 600;
						}
		
						.shop-r {
							width: calc(100% - 20px);
		
							.shop-r-t {
								display: flex;
								justify-content: space-between;
								font-weight: 600;
								margin-bottom: 8px;
		
								.t-name {
									color: #484858;
									overflow: hidden;
									text-overflow: ellipsis;
									display: -webkit-box;
									-webkit-line-clamp: 2;
									-webkit-box-orient: vertical;
								}
		
								.t-price {
									color: #3b9bda;
								}
							}
		
							.shop-r-b {
								display: flex;
								justify-content: space-between;
								font-size: 12px;
		
								.b-xi {
									color: #b1b1b1;
									font-weight: 400;
								}
		
								.b-cu {
									color: #717171;
									font-weight: 500;
								}
		
							}
						}
					}
				}
			}
		
			.closebtn {
				text-align: center;
				height: 34px;
				line-height: 34px;
				color: #358CC9;
				font-size: 17px;
			}
		}
	}
</style>
<style lang="scss">
	page {
		background-color: #fff;
	}
</style>