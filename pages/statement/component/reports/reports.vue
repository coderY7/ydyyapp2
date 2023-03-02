<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack" v-show="ifpage">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
				<view class="navleftview" v-show="!ifpage" @tap="ifpage=true">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</view>
			</view>
			<view class="center">{{option.cxmc}}</view>
			<view class="right">
				<!-- <u-switch v-model="switchValue"  @change="switChange" v-show="!ifpage"></u-switch> -->
				<xuanSwitch :switchList="switchList" :disabled="switchAble" :defaultSwitch="switchValue"
					@change="switChange" v-show="!ifpage"></xuanSwitch>
			</view>
		</view>
		<view class="box-content" v-show="ifpage">
			<view class="form-card">
				<view class="" v-for="(v,i) in queryData">
					<view class="view-flex" v-if="v.type=='开始日期'">
						<text class="form-left-text">{{v.colname}}</text>
						<uni-datetime-picker v-model="v.value" type="date" :clear-icon="!v.ismust" />
						<text class="inp-right-text"></text>
					</view>
					<view class="view-flex" v-if="v.type=='结束日期'">
						<text class="form-left-text">{{v.colname}}</text>
						<uni-datetime-picker v-model="v.value" type="date" :clear-icon="!v.ismust" />
						<text class="inp-right-text"></text>
					</view>
					<view class="view-flex" v-else-if="v.type=='下拉框'" @tap="judgeFunc(v,i,v.type,'ALL')">
						<text class="form-left-text">{{v.colname}}</text>
						<u-input :placeholder="'请选择'+v.colname" v-model="v.value" disabled
							:disabledColor="v.readonly==''?'#fff':'#F5F7FA'">
							<template slot="suffix">
								<view v-if="v.readonly==''&&v.value!=''" @tap.stop="clearAlone(v,i)">
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
								<uni-icons type="clear" size="19" color="#e1e1e1" v-if="v.value!=''"
									@tap="clearAlone(v,i)"></uni-icons>
							</template>
						</u-input>
						<text class="inp-right-text"></text>
					</view>
					<view class="view-flex" v-else-if="v.type=='多选下拉框'">
						<text class="form-left-text">{{v.colname}}</text>
						<u-input :placeholder="'请多选'+v.colname" v-model="v.value" :disabled="v.readonly==''?false:true"
							@change="serchChange">
							<template slot="suffix">
								<view v-if="v.readonly==''&&v.value!=''" @tap.stop="clearAlone(v,i)">
									<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
								</view>
							</template>
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-jiugongge" size="19" color="#8f8f8f"
							@tap="judgeFunc(v,i,v.type,'ALL')">
						</uni-icons>
					</view>
					<view class="view-flex" v-else-if="v.type=='查询下拉框'">
						<text class="form-left-text">{{v.colname}}</text>
						<u-input :placeholder="'请输入查询'+v.colname" v-model="v.value" @change="serchChange">
							<template slot="suffix">
								<uni-icons type="clear" size="19" color="#e1e1e1" v-if="v.readonly==''&&v.value!=''"
									@tap="clearAlone(v,i)"></uni-icons>
							</template>
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-jiugongge" size="19" color="#8f8f8f"
							@tap="judgeFunc(v,i,v.type,'ALL')">
						</uni-icons>
					</view>
					<view class="view-flex" v-else-if="v.type=='选择'" @tap="judgeFunc(v,i,v.type,'ALL')">
						<text class="form-left-text">{{v.colname}}</text>
						<u-input placeholder="请选择" v-model="v.value" disabled disabledColor="#fff">
							<template slot="suffix">
								<view v-if="v.readonly==''&&v.value!=''" @tap.stop="clearAlone(v,i)">
									<uni-icons type="clear" size="19" color="#e1e1e1"></uni-icons>
								</view>
							</template>
						</u-input>
						<text class="inp-right-text"></text>
					</view>
				</view>
				<u-button type="primary" class="my-primary-button" text="查询" throttleTime="1000" @tap="getlist">
				</u-button>
			</view>
		</view>
		<view class="box-content" style="padding-bottom:10px;" v-show="!ifpage">
			<!-- :first-line-fixed="true" -->
			<wyb-table class="wyb-table" :headers="thData" :contents="tableData" :height="tableH" :defaultColWidth="300"
				@longPress="longPress" v-show="switchValue" />
			<view class="paging-card" v-show="!switchValue" @touchstart="touchstart" @touchend="touchend">
				<view :class="{pagingToLeft:pageToLeft,pagingToRight:pageToRight}">
					<view class="card-flex" v-for="(v,key) in cardObj">
						<text class="left-con">{{key}}:</text>
						<text class="right-con">{{v}}</text>
					</view>
					<view class="paging">
						<view>第{{current+1}}页</view>
						<view>/</view>
						<view>共{{tableData.length}}页</view>
					</view>
				</view>
			</view>
		</view>

		<!-- 弹窗。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 -->
		<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
			<scroll-view style="max-height: 60vh;" scroll-y="true">
				<view v-if="ifDrawer=='下拉框'||ifDrawer=='查询下拉框'||ifDrawer=='选择'">
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
</template>

<script>
	import dayjs from "dayjs";
	import xuanSwitch from "@/components/xuan-switch/xuan-switch.vue";
	import {
		Condition,
		Basic,
		Queryall,
		QueryallC,
		QueryMd,
		getlistRC,
	} from "@/network/api.js"
	export default {
		components: {
			xuanSwitch
		},
		data() {
			return {
				option: "",
				queryData: [],
				selectIndex: -1,
				ifDrawer: "",
				popupShow: false,
				selectData: [],
				checkboxValue: [],
				// 第二界面
				ifpage: true,
				switchValue: false,
				switchList: ["表格", "表单"],
				switchAble: false,
				touchstartData: "",
				current: 0,
				cardObj: "",
				pageToLeft:false,
				pageToRight:false,
				//。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。
				thData: [],
				tableData: [],
				tableH: "0px",
			};
		},
		onLoad(option) {
			this.option = option
			this.condition(option.cxbh)
		},
		onReady() {
			// 设置状态栏文字颜色为 白色
			// #ifdef APP-PLUS
			plus.navigator.setStatusBarStyle("light");
			// #endif

			uni.getSystemInfo({
				success: function(res) {
					this.tableH = res.screenHeight - 110 + "px"
				}.bind(this)
			});
		},
		onShow() {
			// uni.addInterceptor('navigateBack', {
			// 	invoke(args) { //触发前
			// 		let arr = args.url.split("/")
			// 		console.log("arrarrarr",arr)
			// 		if (arr[arr.length - 1] == "reports") {
			// 			plus.screen.unlockOrientation()
			// 		} else {
			// 			plus.screen.lockOrientation("portrait-primary")
			// 		}
			// 	},
			// 	complete(res) {
			// 		uni.removeInterceptor('navigateBack')
			// 	}
			// })
		},
		onResize: function(e) {
			this.tableH = e.size.windowHeight - 110 + "px"
		},
		methods: {
			condition(cxbh) {
				uni.showLoading({
					title: "查询中，请稍后"
				});
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"cxbh": cxbh
				}
				console.log("Condition dataes", dataes)
				Condition(dataes).then((res) => {
					console.log("Condition res", res)
					if (res.error_code == 0) {
						this.queryData = res.data
						for (var i in this.queryData) {
							if (this.queryData[i].type == "开始日期") {
								let year = dayjs().year()
								let thisMonth = dayjs().month()
								let monthnum = dayjs().month(thisMonth).toJSON().split("-")[1]
								this.queryData[i].value = `${year}-${monthnum}-01`
							} else if (this.queryData[i].type == "结束日期") {
								this.queryData[i].value = dayjs().format('YYYY-MM-DD')
							} else {
								this.queryData[i].value = ""
							}

							if (this.queryData[i].tabname) {
								this.queryData[i].tabname = JSON.parse(this.queryData[i].tabname)
							}
						}
						console.log("Condition this.queryData", this.queryData)
					} else {
						this.$refs.uToast.show({
							type: "error",
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
				if (state == "多选下拉框" && obj.codeid == "SK") {
					this.checkboxValue = []
					if (this.queryData[index].value.indexOf(",") > -1) {
						this.checkboxValue = this.queryData[index].value.split(",")
					} else {
						this.checkboxValue.push(this.queryData[index].value)
					}
					this.selectIndex = index
					this.ifDrawer = state
					this.popupShow = true
					this.selectData = []
					for (var i in obj.tabname) {
						this.selectData.push({
							"id": obj.tabname[i].id,
							"name": obj.tabname[i].name
						})
					}
					return
				}
				
				if (obj.codeid == "BM") { //部门
					this.queryBmSp(obj, index, state, "1")
				} else if (obj.codeid == "BZ") { //所有部门分组
					this.queryBmSp(obj, index, state, "2")
				} else if (obj.codeid == "DL") { //商品大类
					this.queryBmSp(obj, index, state, "3")
				} else if (obj.codeid == "ZL") { //商品中类
					this.queryBmSp(obj, index, state, "4")
				} else if (obj.codeid == "XL") { //商品小类
					this.queryBmSp(obj, index, state, "5")
				} else if (obj.codeid == "MC") { //所有商户 
					this.querySh(obj, index, state, isAll)
				} else if (obj.codeid == "MD") { //所有门店
					this.queryMd(obj, index, state, isAll)
				} else if (obj.codeid == "FK") { //basic查询
					this.queryMore(obj, index, state, isAll)
				}else{
					
				}
			},
			// 搜索。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。
			serchChange(val) {
				setTimeout(() => {
					if (val != "") {
						for (var i in this.queryData) {
							if (val == this.queryData[i].value) {
								this.queryMore(this.queryData[i], i, this.queryData[i].type, val)
								uni.hideKeyboard()
							}
						}
					}
				}, 2000)
			},
			queryMore(obj, index, state, isAll) {
				if (obj.readonly != "") { //分店
					this.$set(this.queryData[index], "value",
						`${uni.getStorageSync("fdbh")}-${uni.getStorageSync("fdmc")}`)
					this.$forceUpdate()
					return
				}
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"dtype": obj.tabname.type,
					"companyid": uni.getStorageSync("companyid")
				}
				Basic(dataes).then((res) => {
					console.log(obj.tabname.type + " 基本信息basic res", res)
					this.selectIndex = index
					this.ifDrawer = state
					this.popupShow = true
					this.selectData = []
					for (var i in res.data) {
						if (isAll == "ALL") {
							this.selectData.push({
								"id": res.data[i][obj.tabname.id],
								"name": res.data[i][obj.tabname.name]
							})
						} else {
							if (res.data[i][obj.tabname.id].indexOf(isAll) > -1 || res.data[i][obj.tabname.name]
								.indexOf(isAll) > -1) {
								this.selectData.push({
									"id": res.data[i][obj.tabname.id],
									"name": res.data[i][obj.tabname.name]
								})
							}
						}
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			queryBmSp(obj, index, state, levels) {
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
			querySh(obj, index, state, isAll) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token")
				}
				QueryallC(dataes).then((res) => {
					// console.log("QueryallC res",res)
					if (res.error_code == 0) {
						this.selectIndex = index
						this.ifDrawer = state
						this.selectData = []
						this.popupShow = true
						for (var i in res.data) {
							if (isAll == "ALL") {
								this.selectData.push({
									"id": res.data[i].CompanyID,
									"name": res.data[i].CompanyName
								})
							}else{
								if (res.data[i].CompanyID.indexOf(isAll) > -1 || res.data[i].CompanyName.indexOf(isAll) > -1) {
									this.selectData.push({
										"id": res.data[i].CompanyID,
										"name": res.data[i].CompanyName
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
			queryMd(obj, index, state, isAll) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"CompanyID": uni.getStorageSync("companyid")
				}
				QueryMd(dataes).then((res) => {
					console.log("QueryMd res",res)
					if (res.error_code == 0) {
						this.selectIndex = index
						this.ifDrawer = state
						this.selectData = []
						this.popupShow = true
						for (var i in res.data) {
							if (isAll == "ALL") {
								this.selectData.push({
									"id": res.data[i].FDBH,
									"name": res.data[i].FDMC
								})
							}else{
								if (res.data[i].FDBH.indexOf(isAll) > -1 || res.data[i].FDMC.indexOf(isAll) > -1) {
									this.selectData.push({
										"id": res.data[i].FDBH,
										"name": res.data[i].FDMC
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
			getlist() {
				let cons = []
				uni.showLoading({
					title: "搜索中"
				});
				for (var i in this.queryData) {
					if (this.queryData[i].type == "开始日期" || this.queryData[i].type == "结束日期") {
						cons.push({
							"recordid": this.queryData[i].recordid,
							"Convalue": this.queryData[i].value,
						})
					} else {
						cons.push({
							"recordid": this.queryData[i].recordid,
							"Convalue": this.queryData[i].value.split("-")[0],
						})
					}
				}
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"userid": uni.getStorageSync("userid"),
					"djtype": this.option.cxbh,
					"groupid": uni.getStorageSync("groupid"),
					"username": uni.getStorageSync("dlmc"),
					"fdbh": uni.getStorageSync("fdbh"),
					"condition": cons,
				}
				console.log("查询动态报表数据 getlistRC dataes", dataes)
				getlistRC(dataes).then((res) => {
					console.log("查询动态报表数据 getlistRC res", res)
					if (res.error_code == 0) {
						this.ifpage = false
						this.thData = []
						if(res.data.length!=0){
							this.thData.push({
								"key": "hanghao",
								"label": "行号",
								"width": 120,
							})
						}
						for (var i in res.columns) {
							this.thData.push({
								"key": res.columns[i].key,
								"label": res.columns[i].title,
								"width": res.columns[i].width * 2.1,
							})
						}
						this.tableData = res.data
						if(res.data.length!=0){
							this.tableData.push(res.sumdata[0])
							for (var j in this.tableData) {
								if (j == this.tableData.length - 1) {
									this.tableData[j].hanghao = "合计"
								} else {
									this.tableData[j].hanghao = Number(j) + 1
								}
							}
						}
						// this.tableData = this.tableData.slice(0, 10)
						console.log("查询动态报表数据 this.tableData", this.tableData)
						if (this.tableData.length == 0) {
							this.current = -1
						} else {
							this.current = 0
							this.getRowData()
						}
					} else {

					}
					uni.hideLoading();
				}).catch((err) => {
					uni.hideLoading();
					console.log(err)
				})
			},
			getRowData() {
				this.cardObj = this.tableData[this.current]
			},
			// 切换表格或卡片
			switChange(val) {
				this.switchAble = true
				uni.showLoading({
					title: "加载中，请稍后"
				});
				this.switchValue = val
				setTimeout(() => {
					uni.hideLoading()
					this.switchAble = false
				}, 1000)
			},
			// 表格
			longPress(row, index) {
				this.switchAble = true
				uni.showLoading({
					title: "加载中，请稍后"
				});
				this.current = Number(index)
				this.getRowData()
				this.switchValue = false
				setTimeout(() => {
					uni.hideLoading()
					this.switchAble = false
				}, 1000)
			},
			// 卡片
			swiperChange(event) {
				this.current = event.detail.current
			},
			touchstart(event) {
				this.touchstartData = event.changedTouches[0]
			},
			touchend(event) {
				let touchendData = event.changedTouches[0]
				// 不是左右
				if (Math.abs(this.touchstartData.clientY - touchendData.clientY) > Math.abs(this
						.touchstartData.clientX - touchendData.clientX)) {
					return
				} else { //左右滑动
					if (this.touchstartData.clientX < touchendData.clientX) { //向右滑动
						if (this.current > 0) {
							this.pageToRight=true
							setTimeout(()=>{
								this.pageToRight=false
							},400)
							this.current -= 1
							this.getRowData()
						}
					} else { //向左滑动
						if (this.current < this.tableData.length - 1) {
							this.pageToLeft=true
							setTimeout(()=>{
								this.pageToLeft=false
							},400)
							this.current += 1
							this.getRowData()
						}
					}
				}
			},
			// 清除单个数据
			clearAlone(item, index) {
				// console.log("clearAlone item",item)
				this.$set(this.queryData[index], "value", "")
				this.$forceUpdate()
			},

			// popup弹窗控制..................................
			selectChange(id, name) {
				this.queryData[this.selectIndex].value = `${id}-${name}`
				this.popupShow = false
			},
			checkboxChange(event) { //弹窗多选控制
				let str = ""
				event.forEach((v, i, arr) => {
					str += v
					if (i != arr.length - 1) {
						str += ","
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
		watch: {
			ifpage: function(newv, oldv) {
				if (newv) {
					// #ifdef APP-PLUS
					plus.screen.lockOrientation("portrait-primary")
					// #endif
				} else {

				}
			},
			switchValue: function(newv, oldv) {
				// #ifdef APP-PLUS
				if (newv) {
					plus.screen.unlockOrientation()
				} else {
					plus.screen.lockOrientation("portrait-primary")
				}
				// #endif
			},

		}
	}
</script>

<style lang="scss" scoped>
	.box {
		.status-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));
		}

		.nav-bar {
			background-image: linear-gradient(to right, var(--nav-left-color), var(--nav-center-color), var(--nav-right-color));

			.center {
				width: calc(100% - 180px);
			}

			.right {
				width: 80px;
			}
		}

		.box-content {
			padding-top: calc(var(--status-bar-height) + 52px);

			.form-card {
				padding: 10px;
				background-color: #fff;
				border-radius: 10px;
				box-sizing: border-box;
				box-shadow: 0px 1px 5px 3px #e8e8e8;
				position: relative;

				.view-flex {
					width: 100%;
					display: flex;
					justify-content: space-between;
					align-items: center;
				}

				.linear-mask {
					width: 100%;
					height: 100px;
					margin-bottom: 0;
					background-image: linear-gradient(to bottom, rgba(255, 255, 255, 0) 0%, rgba(255, 255, 255, 0.9) 100%);
					position: absolute;
					top: 0;
					left: 0;
				}

				/deep/.uni-date {
					width: calc(100% - 76px);

					.uni-date__x-input {
						height: 38px;
					}

					.uni-date-editor--x .uni-date__icon-clear {
						top: 6px;
						border: 2px solid transparent;
					}

					.uni-date-editor--x__disabled>.uni-date-x {
						background-color: #F5F7FA;
					}
				}

				/deep/.u-input__content__subfix-icon {
					flex-direction: row;
				}

				.inp-right-text {
					display: inline-block;
					width: 19px;
				}

				.form-left-text {
					display: block;
					width: 76px;
				}
			}

			.form-card>* {
				margin-bottom: 10px;
			}

			.u-form.form-card>* {
				margin-bottom: 20px;
			}

			.form-card>*:last-child {
				margin-bottom: 0px;
			}

			/deep/.wyb-table {
				.wyb-table-content-item {
					overflow: hidden;
					text-overflow: ellipsis;
					display: -webkit-box;
					-webkit-line-clamp: 2;
					-webkit-box-orient: vertical;
					word-break: break-all;
				}
			}

			.paging-card {
				padding: 10px;
				border-radius: 20px;
				background-color: #fff;
				box-sizing: border-box;
				box-shadow: 0px 1px 5px 3px #e8e8e8;
				position: relative;

				.card-flex {
					display: flex;
					margin-bottom: 10px;

					.left-con {
						min-width: 80px;
						text-align-last: justify;
						margin-right: 10px;
						color: #747474;
						word-break: keep-all;
					}

					.right-con {
						color: #1c1c28;
					}
				}

				.paging {
					width: 100%;
					padding: 5px 10px;
					background-color: rgba(0, 0, 0, .2);
					box-sizing: border-box;
					display: flex;
					position: sticky !important;
					z-index: 999;
					bottom: 4px;
				}

				.paging>* {
					margin-right: 8px;
				}
			}
		
			.pagingToLeft{
				animation-name: card-move-left;
				animation-duration: 0.3s;
				animation-timing-function: ease-in;
			}
			.pagingToRight{
				animation-name: card-move-right;
				animation-duration: 0.3s;
				animation-timing-function: ease-in;
			}
			
			@keyframes card-move-left{
				0%{
					transform:translateX(0px);
				}
				100%{
					transform:translateX(-100vw);
				}
			}
			@keyframes card-move-right{
				0%{
					transform:translateX(0px);
				}
				100%{
					transform:translateX(100vw);
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

			.u-checkbox {
				margin-bottom: 10px;
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
</style>
<style lang="scss">
	page {
		background-color: #f8f8f8;
	}
</style>