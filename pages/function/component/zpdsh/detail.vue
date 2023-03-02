<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
			</view>
			<view class="center">直配收货:{{option.dh}}</view>
			<view class="right"></view>
		</view>
		<view class="box-content">
			<u-form ref="uForm" :model="uFormModel" :rules="uFormRules">
				<u-form-item label="收货仓库:" :labelWidth="75" prop="shck" @tap="queryMore('CKINFO','shck','')">
					<u-input placeholder="请选择收货仓库" v-model="uFormModel.shck" disabled disabledColor="#fff">
					</u-input>
				</u-form-item>
				<u-form-item label="直配商家:" :labelWidth="75" prop="zpsj">
					<u-input v-model="uFormModel.zpsj" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="订单时间:" :labelWidth="75" prop="ddsj">
					<u-input v-model="uFormModel.ddsj" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="订单类型:" :labelWidth="75" prop="ddlx">
					<u-input v-model="uFormModel.ddlx" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="备注说明:" :labelWidth="75" prop="remark">
					<u-input placeholder="备注说明" v-model="uFormModel.remark" disabled>
					</u-input>
				</u-form-item>
				<u-form-item label="原始单号:" :labelWidth="75" prop="ysdh">
					<u-input placeholder="请输入原始单号" v-model="uFormModel.ysdh">
					</u-input>
				</u-form-item>
				<u-form-item label="税票号码:" :labelWidth="75" prop="sphm">
					<u-input placeholder="请输入税票号码" v-model="uFormModel.sphm">
					</u-input>
				</u-form-item>
				<u-form-item label="物流费用:" :labelWidth="75" prop="wlfyjg">
					<u-input placeholder="物流费用" v-model="uFormModel.wlfyjg">
					</u-input>
				</u-form-item>
				<!-- <view class="image-title">
					<view class="sectionTitle text-black">
						<view class="sectionPic"></view>
						<text>上传附件<text class="text-grey"> (选填，最多可上传1张)</text>
						</text>
					</view>
					<view class="text-grey">{{ imgList.length }}/1</view>
				</view>
				<view class="filepicker">
					<mk-upload :sourceType="['camera','album']" :imgList="imgList" @onDelete="onDelete"
						@onChoose="onChoose" :maxChooseCount="1" :maxCount="1" />
				</view> -->
				
				<u-button type="primary" class="my-primary-button" text="审核" throttleTime="1000" @tap="audit"></u-button>
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
						<view class="multiple-con">
							<text class="left-con">单位:</text>
							<text class="right-con">{{item.dw}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">规格:</text>
							<text class="right-con">{{item.gg}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">原订数量:</text>
							<text class="right-con">{{item.yzpsl}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">原订价格:</text>
							<text class="right-con">￥{{item.yzpjj}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">零售金额:</text>
							<text class="right-con">￥{{item.nsje}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">配调金额:</text>
							<text class="right-con">￥{{item.phje}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con view-flex">
							<text class="left-con">保质期:</text>
							<text class="right-con">{{item.bzqts}}</text>
						</view>
						<view class="multiple-con view-flex">
							<text class="left-con">特供类型:</text>
							<text class="right-con" v-show="state==''||editIndex!=i">{{item.cxjbz}}</text>
							<view v-show="state=='edit'&&editIndex==i" @tap="queryMore('DMINFO','cxtype','')">
								<u-input disabled disabledColor="#fff" v-model="item.cxjbz">
								</u-input>
							</view>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con view-flex">
							<text class="left-con">直配数量:</text>
							<text class="right-con" v-show="state==''||editIndex!=i">{{item.phsl}}</text>
							<u-input placeholder="请输入直配数量" v-model="item.phsl" v-show="state=='edit'&&editIndex==i">
							</u-input>
						</view>
						<view class="multiple-con view-flex">
							<text class="left-con">直配价格:</text>
							<text class="right-con" v-show="state==''||editIndex!=i">￥{{item.phjg}}</text>
							<u-input placeholder="请输入直配价格" v-model="item.phjg" v-show="state=='edit'&&editIndex==i">
							</u-input>
						</view>
					</view>
					<view class="view-flex-row">
						<text class="left-con">生产日期:</text>
						<text class="right-con" v-show="state==''||editIndex!=i">{{item.scrq}}</text>
						<uni-datetime-picker v-model="item.scrq" type="date" :clear-icon="false" v-show="state=='edit'&&editIndex==i"/>
					</view>
					<view class="view-flex-row">
						<text class="left-con">收货备注:</text>
						<text class="right-con" v-show="state==''||editIndex!=i">{{item.shremark}}</text>
						<u-input placeholder="请输入收货备注" v-model="item.shremark" v-show="state=='edit'&&editIndex==i">
						</u-input>
					</view>
					
					<view class="btns" v-show="state=='edit'&&editIndex==i">
						<u-button type="primary" text="保存" throttleTime="1000" @tap="save"></u-button>
						<u-button type="primary" :plain="true" text="取消" @tap="state='',editIndex=-1"></u-button>
					</view>
				</view>
			</view>
			
			<!-- 弹窗。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 -->
			<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
				<scroll-view style="max-height: 60vh;" scroll-y="true">
					<view v-if="ifDrawer=='shck'">
						<view class="list" v-for="(v, i) in selectData" @tap="radioChange(v.id+'-'+v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='cxtype'">
						<view class="list" v-for="(v, i) in selectData" @tap="editChange(v.id)">
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
	import mkupload from '@/components/mk-upload/mk-upload.vue';
	import {
		GetlistC,
		Basic,
		ZpqrdCheck,
		ZpqrdDosave
	} from "@/network/api.js";
	export default {
		components: {
			mkupload
		},
		data() {
			return {
				option:"",
				uFormModel:{
					shck:"",
					ysdh:"",
					sphm:"",
					wlfyjg: "",
					remark:"",
					ddsj:"",
					ddlx:"",
					zpsj:""
				},
				uFormRules: {
					"shck": {
						type: "string",
						required: true,
						message: "请选择仓库",
						trigger: ["blur", "change"]
					}
				},
				ifDrawer:"",
				selectData: [],
				// imgList: [],
				tableData:[],
				popupShow: false,
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
					"djtype": "ZPQRD",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"ztbz": ""
				}
				GetlistC(dataes).then((res) => {
					console.log("获取 直配单收货 已上传的商品 res", res)
					if(res.error_code == 0){
						this.uFormModel.ddsj=res.data[0].zprq
						this.uFormModel.zpsj=res.data[0].sjbh
						this.uFormModel.wlfyjg=res.data[0].wlfyjg
						this.uFormModel.remark=res.data[0].remark
						if(res.data[0].djtype=="C"){
							this.uFormModel.ddlx="ZC-冲帐单"
						}else{
							this.uFormModel.ddlx="ZP-正常单"
						}
						this.queryMore("SJINFO","zpsj",this.uFormModel.zpsj)
						this.tableData=res.data
						for(var i in this.tableData){
							this.tableData[i].scrq=this.tableData[i].scrq.split("T")[0]
						}
					}else{
						this.$refs.uToast.show({
							type:"error",
							message: res.retmsg
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			// 搜索Basic
			queryMore(type,fixid,val) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"dtype": type,
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					console.log(type+"——基本信息basic res", res)
					if (res.error_code == 0) {
						if (val!="") { //自动填充
							for (var i in res.data) {
								if(type=="SJINFO"){
									if (val == res.data[i].sjbh) {
										this.uFormModel[fixid] = `${res.data[i].sjbh}-${res.data[i].sjmc}`
									}
								}else if(type=="CKINFO"){
									if (val == res.data[i].ckbmid) {
										this.uFormModel[fixid] = `${res.data[i].ckbmid}-${res.data[i].ckmc}`
									}
								}
							}
						} else {
							this.selectData = []
							this.ifDrawer = fixid
							this.popupShow = true
							for (var i in res.data) {
								if(type=="CKINFO"){
									this.selectData.push({
										"id": res.data[i].ckbmid,
										"name": res.data[i].ckmc
									})
								}else if(type=="DMINFO"){
									this.selectData.push({
										"id": res.data[i].sjcxlxid,
										"name": res.data[i].lxmc
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
			radioChange(event){
				this.uFormModel.shck=event
				this.popupShow=false
			},
			audit(){
				this.$refs.uForm.validate().then(res => {
					let dataes={
						"access_token": uni.getStorageSync("access_token"),
						"userid": uni.getStorageSync("userid"),
						"username": uni.getStorageSync("dlmc"),
						"djbh": this.option.dh,
						"fdbh": uni.getStorageSync("fdbh"),
						"ydhrq": "",
						"remark": this.uFormModel.remark,
					}
					ZpqrdCheck(dataes).then((res) => {
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
				})
			},
			
			toedit(item,index){
				this.state="edit"
				this.editIndex=index
			},
			editChange(event){
				this.$set(this.tableData[this.editIndex], "cxjbz", event)
				this.$forceUpdate()
				this.popupShow=false
			},
			save(){
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djbh": this.option.dh,
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"ysdh": this.uFormModel.ysdh,
					"sphm": this.uFormModel.sphm,
					"vtype": "EDIT",
					"list":[{
						"spbm": this.tableData[this.editIndex].spbm,
						"spsmm": this.tableData[this.editIndex].spsmm,
						"spmc": this.tableData[this.editIndex].spmc,
						"zpjj": this.tableData[this.editIndex].phjg,
						"zpsl": this.tableData[this.editIndex].phsl,
						"guid": this.tableData[this.editIndex].guid,
						"bzqts": this.tableData[this.editIndex].bzqts,
						"scrq": this.tableData[this.editIndex].scrq,
						"cxbz": this.tableData[this.editIndex].cxjbz,
						"shbz": this.tableData[this.editIndex].shremark,
					}]
				}
				console.log("保存数据 dataes",dataes)
				// ZpqrdDosave(dataes).then((res) => {
				// 	console.log("保存数据 res",res)
				// 	if (res.error_code == 0) {
				// 		this.$refs.uToast.show({
				// 			type: "success",
				// 			message: "保存成功"
				// 		})
				// 		this.state=""
				// 		this.editIndex=-1
				// 	} else {
				// 		this.$refs.uToast.show({
				// 			type: "error",
				// 			message: res.message
				// 		})
				// 	}
				// }).catch((err) => {
				// 	console.log(err)
				// })
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
	.box{
		.status-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}
		.nav-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}
		
		.box-content {
			padding-top: calc(var(--status-bar-height) + 52px);
			
			/deep/.u-form{
				.u-button{
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
					.view-flex{
						display: flex;
						align-items: center;
					}
					.view-flex-row{
						width: 100%;
						display: flex;
						align-items: center;
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