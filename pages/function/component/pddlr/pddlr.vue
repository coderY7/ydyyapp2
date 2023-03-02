<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
			</view>
			<view class="center">配调单录入</view>
			<view class="right">
				<u-button class="icon-button guideJS1" text="" throttleTime="2000" @tap="newOrder">
					<uni-icons type="plusempty" size="30" color="#fff"></uni-icons>
				</u-button>
			</view>
		</view>
		<view class="box-content">
			<view class="form-card">
				<view class="" v-for="(v,i) in queryData">
					<view class="view-flex" v-if="v.type=='开始DATE'">
						<text class="form-left-text">{{v.colname}}</text>
						<uni-datetime-picker v-model="v.value" type="date" :clear-icon="!v.ismust"/>
						<text class="inp-right-text"></text>
					</view>
					<view class="view-flex" v-if="v.type=='结束DATE'">
						<text class="form-left-text">{{v.colname}}</text>
						<uni-datetime-picker v-model="v.value" type="date" :clear-icon="!v.ismust"/>
						<text class="inp-right-text"></text>
					</view>
					<view v-show="foldMoreShow">
						<view class="view-flex" v-if="v.type=='查询下拉框'">
							<text class="form-left-text">{{v.colname}}</text>
							<u-input :placeholder="'请输入'+v.colname" :disabled="v.readonly==''?false:true" v-model="v.value" @change="inpChange">
								<template slot="suffix">
									<uni-icons type="clear" size="19" color="#e1e1e1" v-if="v.readonly==''&&v.value!=''" @tap="clearAlone(v,i)"></uni-icons>
								</template>
							</u-input>
							<uni-icons custom-prefix="iconfont" class="guideJS2" type="icon-jiugongge" size="19" color="#8f8f8f" @tap="judgeFunc(v,i,v.type,'ALL')">
							</uni-icons>
						</view>
						<view class="view-flex" v-else-if="v.type=='下拉框'" @tap="judgeFunc(v,i,v.type,'ALL')">
							<text class="form-left-text">{{v.colname}}</text>
							<!-- :disabledColor="v.readonly==''?'#fff':'#F5F7FA'" -->
							<u-input :placeholder="'请选择'+v.colname" disabled disabledColor="#fff" v-model="v.value">
								<template slot="suffix">
									<!-- v-if="v.readonly==''&&v.value!=''" -->
									<view v-if="v.value!=''" @tap.stop="clearAlone(v,i)">
										<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
									</view>
								</template>
							</u-input>
							<text class="inp-right-text"></text>
						</view>
						<view class="view-flex" v-else-if="v.type=='字符'">
							<text class="form-left-text">{{v.colname}}</text>
							<u-input :placeholder="'请输入'+v.colname" v-model="v.value">
								<template slot="suffix">
									<uni-icons type="clear" size="19" color="#e1e1e1" v-if="v.value!=''" @tap="clearAlone(v,i)"></uni-icons>
								</template>
							</u-input>
							<text class="inp-right-text"></text>
						</view>
						<view class="view-flex" v-else-if="v.type=='多选下拉框'">
							<text class="form-left-text">{{v.colname}}</text>
							<u-input :placeholder="'请多选'+v.colname" :disabled="v.readonly==''?false:true" v-model="v.value" @change="inpChange">
								<template slot="suffix">
									<uni-icons type="clear" size="19" color="#e1e1e1" v-if="v.readonly==''&&v.value!=''" @tap="clearAlone(v,i)"></uni-icons>
								</template>
							</u-input>
							<uni-icons custom-prefix="iconfont" type="icon-jiugongge" size="19" color="#8f8f8f" @tap="judgeFunc(v,i,v.type,'ALL')">
							</uni-icons>
						</view>
					</view>
				</view>
				<view class="linear-mask" v-if="!foldMoreShow"></view>
				<u-button type="primary" class="my-primary-button" text="查询" throttleTime="2000" @tap="search">
				</u-button>
				<view class="fold-more" @tap="foldMoreShow=!foldMoreShow">
					<text v-if="foldMoreShow">收起</text>
					<text v-else>展开</text>
					<uni-icons type="top" size="19" color="#3386c4" v-if="foldMoreShow"></uni-icons>
					<uni-icons type="bottom" size="19" color="#3386c4" v-else></uni-icons>
				</view>
			</view>
			
			<!-- 查询后的 内容 -->
			<view class="foldGroup">
				<view class="fold-title" v-for="(v,i) in tableData" @tap="tolook(v)">
					<view class="fold-title-t fold-title-flex-start">
						<text>{{v.配调单编号}}</text>
					</view>
					<view class="fold-title-flex-start fold-title-con show-dots">
						<text class="left-con">收货分店:</text>
						<text class="right-con">{{v.收货分店}}</text>
					</view>
					<view class="fold-title-flex-start fold-title-con show-dots">
						<text class="left-con">出货分店:</text>
						<text class="right-con">{{v.出货分店}}</text>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">出货仓库:</text>
							<text class="right-con">{{v.出货仓库}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">chfdbh:</text>
							<text class="right-con">{{v.chfdbh}}</text>
						</view>
					</view>
					<view class="multiples">
						<view class="multiple-con">
							<text class="left-con">配调数量:</text>
							<text class="right-con">{{v.配调数量}}</text>
						</view>
						<view class="multiple-con">
							<text class="left-con">审核状态:</text>
							<text class="right-con" v-if="v.审核状态=='已审核'" style="color:green;">{{v.审核状态}}</text>
							<text class="right-con" v-else style="color:red;">{{v.审核状态}}</text>
						</view>
					</view>
				</view>
			</view>
			
			<!-- 弹窗。。。。。。。。。。 -->
			<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
				<scroll-view style="max-height: 60vh;" scroll-y="true">
					<view v-if="ifDrawer=='下拉框'||ifDrawer=='查询下拉框'">
						<view class="list" v-for="(v, i) in selectData" @tap="selectChange(v.id,v.name)">
							<text>{{v.id}}</text>-
							<text>{{v.name}}</text>
						</view>
					</view>
					<view v-else-if="ifDrawer=='多选下拉框'">
						<u-checkbox-group v-model="checkboxValue" placement="column" @change="checkboxChange">
							<u-checkbox v-for="(v, i) in selectData" :key="v.id" :label="v.id+'-'+v.name" :name="v.id">
							</u-checkbox>
						</u-checkbox-group>
					</view>
				</scroll-view>
				<view class="closebtn" @tap="popupShow=false">确定</view>
			</u-popup>
			<u-toast ref="uToast"></u-toast>
			
			
		</view>
	</view>
</template>

<script>
	import dayjs from 'dayjs';
	import {
		Setupinfo,
		Condition,
		Queryall,
		Basic,
		GetlistF,
		OrderNew
	} from "@/network/api.js";
	export default {
		data() {
			return {
				djtype:"",
				queryData:[],
				foldMoreShow:true,
				selectIndex:-1,
				ifDrawer:"",
				selectData:[],
				checkboxValue:[],
				tableData:[],
				popupShow:false,
			}
		},
		onLoad() {
			this.setupinfo()
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
			setupinfo(){
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"fdbh": uni.getStorageSync("fdbh"),
					"tabid": "2",
					"codeid": "PHDNeedSortCheckOk",
				}
				Setupinfo(dataes).then((res) => {
					// console.log("查询SetupInfo表 res",res)
					if(res.error_code==0){
						this.djtype = res.message == "T" ? "PHDSORT" : "PHDBH"
						this.condition()
					}else{
						this.$refs.uToast.show({
							type: "error",
							message: "查询单据类型失败"
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			//拉取查询条件
			condition(){
				uni.showLoading({
					title: "查询中，请稍后"
				});
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"cxbh": this.djtype+"_F",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"username": uni.getStorageSync("dlmc"),
				}
				Condition(dataes).then((res) => {
					console.log("配调单 condition res",res)
					if(res.error_code==0){
						this.queryData=res.data
						for(var i in this.queryData){
							let now=dayjs().format('YYYY-MM-DD')
							if(this.queryData[i].type=='开始DATE'){
								this.queryData[i].value="2022-07-31"
							}else if(this.queryData[i].type=='结束DATE'){
								this.queryData[i].value=now
							}else{
								this.queryData[i].value=""
							}
							if(this.queryData[i].tabname){
								this.queryData[i].tabname=JSON.parse(this.queryData[i].tabname)
							}
							
							// if(this.queryData[i].readonly!=""){
							// 	this.queryMore(this.queryData[i],i,this.queryData[i].type,'ALL')
							// }
						}
						console.log("condition this.queryData",this.queryData)
					}else{
						this.$refs.uToast.show({
							type:"error",
							message: res.message
						})
					}
					uni.hideLoading()
				}).catch((err) => {
					uni.hideLoading()
					console.log(err)
				})
			},
			
			judgeFunc(obj, index, state, isAll) {
				if (obj.codeid == "BM") { //部门
					this.queryBmSp(obj, index, state, "1", isAll)
				} else if (obj.codeid == "FK") { //basic查询
					this.queryMore(obj, index, state, isAll)
				} else if (obj.codeid == "SK") { //basic查询
					this.queryMore(obj, index, state, isAll)
				}else{
					
				}
			},
			// 监听所有查询下拉框 变化
			inpChange(val){
				setTimeout(() => {
					if (val != "") {
						for(var i in this.queryData){
							if(val==this.queryData[i].value){
								if (this.queryData[i].codeid == "BM") { //部门
									this.queryBmSp(this.queryData[i], i, this.queryData[i].type, "1", val)
								} else if (this.queryData[i].codeid == "FK") { //basic查询
									this.queryMore(this.queryData[i], i, this.queryData[i].type, val)
								}
								uni.hideKeyboard()
							}
						}
					}
				}, 2000)
			},
			// 查询条件中的单个查询.................................
			queryMore(obj,index,state,isAll){
				if(state=="多选下拉框"&&obj.codeid=="SK"){
					this.checkboxValue=[]
					if(this.queryData[index].value.indexOf(",")>-1){
						this.checkboxValue=this.queryData[index].value.split(",")
					}else{
						this.checkboxValue.push(this.queryData[index].value)
					}
					this.selectIndex=index
					this.ifDrawer=state
					this.popupShow=true
					this.selectData=[]
					for(var i in obj.tabname){
						this.selectData.push({
							"id":obj.tabname[i].id,
							"name":obj.tabname[i].name
						})
					}
					return
				}
				// if(obj.readonly!=""){//分店
				// 	this.$set(this.queryData[index], "value", `${uni.getStorageSync("fdbh")}-${uni.getStorageSync("fdmc")}`)
				// 	this.$forceUpdate()
				// 	return
				// }
				
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"dtype": obj.tabname.type,
					"companyid": uni.getStorageSync("companyid")
				}
				Basic(dataes).then((res) => {
					console.log(obj.tabname.type+" 基本信息basic res",res)
					this.selectIndex=index
					this.ifDrawer=state
					this.popupShow=true
					this.selectData=[]
					for(var i in res.data){
						if(isAll=="ALL"){
							this.selectData.push({
								"id":res.data[i][obj.tabname.id],
								"name":res.data[i][obj.tabname.name]
							})
						}else{
							if(res.data[i][obj.tabname.id].indexOf(isAll)>-1||res.data[i][obj.tabname.name].indexOf(isAll)>-1){
								this.selectData.push({
									"id":res.data[i][obj.tabname.id],
									"name":res.data[i][obj.tabname.name]
								})
							}
						}
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			queryBmSp(obj, index, state, levels, isAll) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"CompanyID": uni.getStorageSync("companyid"),
					"fdbh": uni.getStorageSync("fdbh"),
					"level": levels,
					"ParentsID": ""
				}
				Queryall(dataes).then((res) => {
					console.log(levels+" queryBmSp res",res)
					if (res.error_code == 0) {
						this.selectIndex = index
						this.ifDrawer = state
						this.selectData = []
						this.popupShow = true
						for (var i in res.nodes) {
							if (isAll == "ALL") {
								this.selectData.push({
									"id": res.nodes[i].id,
									"name": res.nodes[i].title
								})
							} else {
								if (res.nodes[i].id.indexOf(isAll) > -1 || res.nodes[i].title.indexOf(isAll) > -1) {
									this.selectData.push({
										"id": res.nodes[i].id,
										"name": res.nodes[i].title
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
			
			search(){
				this.tableData = []
				let str="'"+this.djtype+"',"
				for(var i in this.queryData){
					if(this.queryData[i].type=="查询下拉框"||this.queryData[i].type=="下拉框"){
						str+="'"+this.queryData[i].value.split("-")[0]+"'"
					}else if(this.queryData[i].type=="多选下拉框"){
						if(this.queryData[i].value!=""){
							let ztArr=this.queryData[i].value.split(",")
							let ztStr=`'`
							ztArr.forEach((v,i,arr)=>{
								ztStr+=`\"${v}\"`
								if(i!=arr.length-1){
									ztStr+=","
								}
							})
							ztStr+=`'`
							str+=ztStr
						}else{
							str+=`''`
						}
					}else{
						str+="'"+this.queryData[i].value+"'"
					}
					if(i!=this.queryData.length-1){
						str+=","
					}
				}
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djtype": this.djtype,
					"exeStr": str,
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
				}
				console.log("！！查询已上传的数据！！ dataes",dataes)
				GetlistF(dataes).then((res) => {
					console.log("！！查询已上传的数据！！ res", res)
					if (res.error_code == 0) {
						this.foldMoreShow=false
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
			// 新增单据
			newOrder(){
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djtype": this.djtype,
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
				}
				OrderNew(dataes).then((res) => {
					// console.log("orderNew res",res)
					if (res.error_code == 0) {
						uni.navigateTo({
							url: `/pages/function/component/pddlr/pddxd?dh=${res.djbh}&orderType=add&title=未审核&djtype=${this.djtype}`
						});
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: "获取新单号失败，请稍后再试"
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			
			// 扫码 搜索商品
			// scan() {
			// 	uni.scanCode({
			// 		success: (res) => {
			// 			console.log('扫码内容', res)
			// 			this.uFormModel.spsmm=res.result
			// 		},
			// 		fail: (err) => {
			// 			this.$refs.uToast.show({
			// 				type:"error",
			// 				message: "识别失败"
			// 			})
			// 		}
			// 	});
			// }, 
			
			// 编辑单据
			tolook(event){
				// console.log("rowClick event",event)
				let reg = /[^\u4e00-\u9fa5\uf900-\ufa2d]/g;
				let ztbz=event.审核状态.replace(reg, '')
				let tempType=""
				if(ztbz=="未审核"){
					tempType="edit"
				}else{
					tempType="look"
				}
				uni.navigateTo({
					url: `/pages/function/component/pddlr/pddxd?dh=${event.配调单编号}&djtype=${this.djtype}&orderType=${tempType}&title=${ztbz}&chfd=${event.出货分店}&shfd=${event.收货分店}&chck=${event.出货仓库}&pdsl=${event.配调数量}&chfdbh=${event.chfdbh}`
				});
			},
			
			// 清除
			clearAlone(obj,index){
				this.$set(this.queryData[index], "value", "")
				this.$forceUpdate()
			},
			// popup弹窗控制..................................
			selectChange(id,name){
				this.queryData[this.selectIndex].value=`${id}-${name}`
				this.popupShow=false
			},
			checkboxChange(event){//弹窗多选控制
				let str=""
				event.forEach((v,i,arr)=>{
					str+=v
					if(i!=arr.length-1){
						str+=","
					}
				})
				this.$set(this.queryData[this.selectIndex], "value", str)
				this.$forceUpdate()
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
			
			.form-card{
				padding: 10px;
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
				.linear-mask{
					width: 100%;
					height: 100px;
					margin-bottom: 0;
					background-image: linear-gradient(to bottom, rgba(255,255,255,0) 0%, rgba(255,255,255,0.9) 100%);
					position: absolute;
					top: 0;
					left: 0;
				}
				/deep/.uni-date{
					width: calc(100% - 95px);
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
				margin-bottom: 10px;
			}
			.form-card>*:last-child{
				margin-bottom: 0px;
			}
			.fold-more{
				color: var(--nav-center-color);
				display: flex;
				justify-content: center;
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
			
			.u-popup__content{
				padding-left: 15px;
				padding-right: 15px;
				padding-bottom: 10px;
				border-radius: 20px;
			}
			.uni-scroll-view-content {
				box-sizing: border-box;
				padding: 10px 0;
		
				.list{
					padding:10px;
					border-bottom: 1px solid #E6E6E6;
				}
				.list>uni-text:first-child{
					color: #4A4A4A;
				}
				.list>uni-text:last-child{
					color: #A9A9A9;
				}
				
				.u-checkbox {
					margin-bottom: 10px;
				}
			}
			
			.closebtn{
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