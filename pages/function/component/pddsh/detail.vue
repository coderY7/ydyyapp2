<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
			</view>
			<view class="center">配调收货:{{option.dh}}</view>
			<view class="right"></view>
		</view>
		<view class="box-content">
			<u-form ref="uForm" :model="uFormModel" :rules="uFormRules">
				<u-form-item label="调出分店:" :labelWidth="75" prop="dcfd">
					<u-input placeholder="请选择调出分店" v-model="uFormModel.dcfd" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="收货分店:" :labelWidth="75" prop="shfd">
					<u-input placeholder="请选择收货分店" v-model="uFormModel.shfd" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="收货仓库:" :labelWidth="75" prop="shck" @tap="queryMore('','CKINFO','shck')">
					<u-input placeholder="请选择收货仓库" v-model="uFormModel.shck" disabled disabledColor="#fff">
					</u-input>
				</u-form-item>
				<u-form-item label="配调类型:" :labelWidth="75" prop="pdlx">
					<u-input placeholder="请选择配调类型" v-model="uFormModel.pdlx" color="pdlxColor" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="配调时间:" :labelWidth="75" prop="pdsj">
					<u-input placeholder="请选择配调时间" v-model="uFormModel.pdsj" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="被冲单号:" :labelWidth="75" prop="bcdh">
					<u-input v-model="uFormModel.bcdh">
					</u-input>
				</u-form-item>
				<u-form-item label="备注说明:" :labelWidth="75" prop="remark">
					<u-input v-model="uFormModel.remark">
					</u-input>
				</u-form-item>
				<u-form-item label="收货状态:" :labelWidth="75" prop="shzt">
					<u-input placeholder="请选择收货状态" v-model="uFormModel.shzt" disabled>
					</u-input>
				</u-form-item>

				<u-button type="primary" class="my-primary-button" text="审核" throttleTime="1000" @tap="audit">
				</u-button>
			</u-form>

			<!-- 查询后的 内容 -->
			<view class="foldGroup">
				<view class="fold-content" v-for="(item,i) in tableData">
					<view class="card-flex-wrap">
						<view class="card-row">{{item.spmc}}</view>
						<view>
							<uni-icons type="compose" size="20" color="#3183C1" @tap="toedit(item,i)"></uni-icons>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">编码:</text>
							<text class="right-con">{{item.spbm}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">条码:</text>
							<text class="right-con">{{item.spsmm}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con view-flex">
							<text class="left-con">单位:</text>
							<text class="right-con">{{item.dw}}</text>
						</view>
						<view class="multiple-con view-flex">
							<text class="left-con">规格:</text>
							<text class="right-con">￥{{item.gg}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con view-flex">
							<text class="left-con">配调数量:</text>
							<text class="right-con">{{item.phsl}}</text>
						</view>
						<view class="multiple-con view-flex">
							<text class="left-con">配调价格:</text>
							<text class="right-con">￥{{item.phjg}}</text>
						</view>
					</view>
					<view class="card-row">
						<text class="left-con">收货备注:</text>
						<text class="right-con" v-show="state==''">{{item.shremark}}</text>
						<u-input placeholder="请输入收货备注" v-model="item.shremark" v-show="state=='edit'&&editIndex==i">
						</u-input>
					</view>
					<view class="btns" v-show="state=='edit'&&editIndex==i">
						<u-button type="primary" text="保存" throttleTime="1000" @tap="save"></u-button>
						<u-button type="primary" :plain="true" text="取消" @tap="state='',editIndex=-1"></u-button>
					</view>
				</view>
			</view>

			<!-- 弹窗。。。。。。。。。。 -->
			<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
				<scroll-view style="max-height: 60vh;" scroll-y="true">
					<view v-if="ifDrawer=='dcfd'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange('dcfd',v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='shfd'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange('shfd',v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='shck'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange('shck',v.id+'-'+v.name)">
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
		GetlistC,
		Basic,
		DbqrdCheck,
		DbqrdDosave
	} from "@/network/api.js";
	export default {
		data() {
			return {
				option: "",
				uFormModel: {
					dcfd: "",
					shfd: "",
					shck: "",
					pdlx: "",
					pdsj: "",
					bcdh: "",
					shzt: "",
					remark: "",
				},
				uFormRules: {
					"shck": {
						type: "string",
						required: true,
						message: "请选择仓库",
						trigger: ["blur", "change"]
					}
				},
				pdlxColor: "",
				ifDrawer: "",
				selectData: [],
				popupShow: false,
				tableData: [],
				state: "",
				editIndex: -1,

			}
		},
		onLoad(option) {
			this.option = option
			this.orderInfo(option.dh)
		},
		onReady() {
			// #ifdef APP-PLUS
			plus.navigator.setStatusBarStyle("light");
			// #endif
		},
		onShow() {

		},
		methods: {
			orderInfo(orderid) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"djbh": orderid,
					"djtype": "PHQRD",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"ztbz": ""
				}
				GetlistC(dataes).then((res) => {
					console.log("获取该单号已上传的商品 res", res)
					if (res.error_code == 0) {
						if (res.data[0].djtype) {
							if (res.data[0].djtype == "T") {
								this.uFormModel.pdlx = "PH-正常单"
								this.pdlxColor = "#000"
							} else if (res.data[0].djtype == "C") {
								this.uFormModel.pdlx = "PC-冲帐单"
								this.pdlxColor = "#ff0000"
							} else if (res.data[0].djtype == "B") {
								this.uFormModel.pdlx = "PB-追补单"
								this.pdlxColor = "#ff0000"
							} else if (res.data[0].djtype == "K") {
								this.uFormModel.pdlx = "PK-退库单"
								this.pdlxColor = "#000"
							}
						}
						//注入订单信息
						this.uFormModel.shck = res.data[0].shckbm
						this.uFormModel.dcfd = res.data[0].chfdbh
						this.uFormModel.shfd = res.data[0].shfdbh
						this.uFormModel.remark = res.data[0].remark

						this.uFormModel.pdsj = res.data[0].bzjzrq
						this.uFormModel.bcdh = res.data[0].bcdh
						this.uFormModel.shzt = "未收货上账确认"
						this.queryMore(this.uFormModel.dcfd,"FDINFO","dcfd")
						this.queryMore(this.uFormModel.shfd,"FDINFO","shfd")
						this.queryMore(this.uFormModel.shck,"CKINFO","shck")
						this.tableData = res.data
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
			// 搜索仓库和分店
			queryMore(val,type,fixid) {
				this.ifDrawer = fixid
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"dtype": type,
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					// console.log(type+"——基本信息basic res", res)
					if (res.error_code == 0) {
						if (val!="") { //自动填充
							for (var i in res.data) {
								if (type == "FDINFO") {
									if (val == res.data[i].fdbh) {
										this.uFormModel[fixid] = `${res.data[i].fdbh}-${res.data[i].fdmc}`
									}
								} else if (type == "CKINFO") {
									if (val == res.data[i].ckbmid) {
										this.uFormModel[fixid] = `${res.data[i].ckbmid}-${res.data[i].ckmc}`
									}
								}
							}
						} else {
							this.selectData = []
							this.popupShow = true
							if (type == "FDINFO") {
								for (var i in res.data) {
									this.selectData.push({
										"id": res.data[i].fdbh,
										"name": res.data[i].fdmc
									})
								}
							} else if (type == "CKINFO") {
								for (var i in res.data) {
									this.selectData.push({
										"id": res.data[i].ckbmid,
										"name": res.data[i].ckmc
									})
								}
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
			radioChange(id,event) {
				this.uFormModel[id] = event
				this.popupShow=false
			},
			audit() {
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djbh": this.option.dh,
					"djtype": "T",
					"fdbh": uni.getStorageSync("fdbh"),
					"shck": this.uFormModel.shck,
					"userid": uni.getStorageSync("userid"),
					"username": uni.getStorageSync("dlmc")
				}
				DbqrdCheck(dataes).then((res) => {
					if (res.error_code == 0) {
						this.$refs.uToast.show({
							type: "success",
							message: "审核单据成功"
						})
						setTimeout(() => {
							uni.navigateBack({
								delta: 1
							})
						}, 1800)
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
			toedit(item,index){
				this.state="edit"
				this.editIndex=index
			},
			save(){
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djbh": this.option.dh,
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"vtype": "EDIT",
					"list":[{
						"phdhh": this.tableData[this.editIndex].phdhh,
						"spbm": this.tableData[this.editIndex].spbm,
						"spmc": this.tableData[this.editIndex].spmc,
						"spsmm": this.tableData[this.editIndex].spsmm,
						"shremark": this.tableData[this.editIndex].shremark,
					}]
				}
				console.log("保存数据 dataes",dataes)
				DbqrdDosave(dataes).then((res) => {
					console.log("保存数据 res",res)
					if (res.error_code == 0) {
						this.$refs.uToast.show({
							type: "success",
							message: "保存成功"
						})
						this.state=""
						this.editIndex=-1
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.message
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			}


		},
		computed: {
			skin() {
				return this.$store.state.skin;
			}
		},
	}
</script>

<style lang="scss" scoped>
	.box {
		.status-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}

		.nav-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}

		.box-content {
			padding-top: calc(var(--status-bar-height) + 46px);

			/deep/.u-form {
				.u-form-item__body{
				    padding: 7px 0 !important;
				}
				.u-button {
					margin-top: 10px;
					margin-bottom: 10px;
				}
			}

			.foldGroup {
				.fold-content {
					margin-bottom: 10px;
					border-radius: 10px;
					box-sizing: border-box;
					padding: 10px;
					box-shadow: 0px 1px 5px 3px #e8e8e8;
					background-color: #fff;

					.card-flex-wrap {
						display: flex;
						justify-content: space-between;
						align-items: center;

						// flex-wrap: wrap;
						.card-row {
							font-size: 16px;
							color: #1c1c28;
							font-weight: 600;
							overflow: hidden;
							text-overflow: ellipsis;
							display: -webkit-box;
							-webkit-line-clamp: 2;
							-webkit-box-orient: vertical;
						}
					}

					.multiples {
						width: 100%;
						font-size: 14px;
						display: flex;
						justify-content: space-between;
						align-items: center;

						.multiple-con {
							width: 50%;
						}
					}
					
					.left-con {
						font-size: 12px;
						color: #999;
						font-weight: 400;
						margin-right: 4px;
						word-break: keep-all;
					}
					
					.right-con {
						color: #1c1c28;
					}
				}

				.fold-content>* {
					margin-bottom: 10px;
				}

				.fold-content>*:last-child {
					margin-bottom: 0px;
				}
			}
		
			.btns{
				display: flex;
				justify-content: space-between;
				
				/deep/.u-button--primary {
					width: 33%;
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