<template>
	<view class="box" :style="skin">
		<view class="status-bar"></view>
		<view class="nav-bar">
			<view class="left">
				<navigator open-type="navigateBack" v-show="ifpage">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</navigator>
				<view class="navleftview" v-show="!ifpage" @tap="toback">
					<uni-icons type="back" size="30" color="#fff"></uni-icons>
				</view>
			</view>
			<view class="center" v-show="ifpage">盘点新单</view>
			<view class="center" v-show="!ifpage">商品明细</view>
			<view class="right" v-show="ifpage">
				<u-button class="icon-button" text="" throttleTime="2000" :disabled="state=='add'" @tap="newOrder">
					<uni-icons type="plusempty" size="30" color="#fff"></uni-icons>
				</u-button>
			</view>
		</view>
		<view class="box-content" v-show="ifpage">
			<!-- 新增单据 表头 -->
			<view class="my-collapse">
				<view class="my-collapse-title" @tap="myCollShow=!myCollShow">
					<view class="fold-title">
						<view class="fold-title-t fold-title-flex">
							<text>单号:{{uFormTitle.djbh}}</text>
							<text class="dhliang" @tap.stop="ifpage=false">明细:{{tableData.length}}</text>
						</view>
						<view style="display:flex;justify-content:center;align-items:center;padding:8px;">
							<text v-if="myCollShow">收起</text>
							<text v-else>展开</text>
							<uni-icons type="top" size="19" color="#fff" v-if="myCollShow"></uni-icons>
							<uni-icons type="bottom" size="19" color="#fff" v-else></uni-icons>
						</view>
					</view>
				</view>
				<view class="my-collapse-con" v-show="myCollShow">
					<view style="text-align:center;color:#F56C6C;" v-if="neworderShow">这是一个新单！！！</view>
					<u-form class="form-card" labelPosition="left" :model="uFormTitle">
						<u-form-item label="盘点类型" :labelWidth="76" prop="pdlx">
							<!-- :disabledColor="state=='pladd'||state=='edit'||state=='look'||state=='check'?'#F5F7FA':'#fff'" -->
							<u-input placeholder="请选择盘点类型" disabled v-model="uFormTitle.pdlx">
							</u-input>
						</u-form-item>
						<u-form-item label="盘点仓库" :labelWidth="76" prop="pdck" @tap="queryCk(false,'')">
							<u-input placeholder="请选择盘点仓库" disabled
								:disabledColor="state=='pladd'||state=='edit'||state=='look'||state=='check'?'#F5F7FA':'#fff'"
								v-model="uFormTitle.pdck">
							</u-input>
						</u-form-item>
						<u-form-item label="盘点门店" :labelWidth="76" prop="pdfd" @tap="queryMore(false,'FDINFO','pdfd')">
							<u-input placeholder="请选择盘点门店" disabled
								:disabledColor="state=='pladd'||state=='edit'||state=='look'||state=='check'?'#F5F7FA':'#fff'"
								v-model="uFormTitle.pdfd">
							</u-input>
						</u-form-item>
					</u-form>
					<view class="flex-btns">
						<u-button type="primary" class="my-primary-button" text="报审"
							:disabled="state=='add'||state=='look'||state=='check'" throttleTime="2000"
							@tap="subCheck">
						</u-button>
						<u-button type="primary" class="my-primary-button" text="删单"
							:disabled="state=='add'||state=='look'||state=='check'" throttleTime="2000" @tap="deldh">
						</u-button>
						<u-button type="primary" class="my-primary-button" text="新增明细" :disabled="state=='look'||state=='check'"
							throttleTime="2000" @tap="getcolumns">
						</u-button>
					</view>
				</view>
			</view>
			<!-- 新增单据 内容 -->
			<view v-show="contentShow">
				<u-form class="form-card" labelPosition="left" :model="uFormModel" :rules="uFormRules" ref="uForm">
					<u-form-item label="商品编码" :labelWidth="74" prop="spbm" class="shoping" v-show="doingindex>=0">
						<u-input placeholder="请输入商品编码/名称/简码" :disabled="isVoiceMode" v-model="uFormModel.spbm" @change="spbmChange" :focus="focusObj.spbmFocus">
							<template slot="suffix">
								<uni-icons type="clear" size="19" color="#e1e1e1" v-if="uFormModel.spbm!=''"
									@tap="clearAlone('spbm')"></uni-icons>
							</template>
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='shoping'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('shoping',false)"></uni-icons>
						<uni-icons custom-prefix="iconfont" type="icon-shouye" size="20" color="#606060"
							@tap="scan()" v-if="!isVoiceMode&&!ispda"></uni-icons>
					</u-form-item>
					<view>
						<view class="shopTishi">
							<view class="shopTishi-view show-dots" v-if="uFormModel.spmc">
								<text class="left-con">名称-条码:</text>
								<text>{{uFormModel.spmc}}</text>-
								<text>{{uFormModel.spsmm}}</text>
							</view>
						</view>
						<view class="shopTishi">
							<view class="shopTishi-view-half" v-if="uFormModel.dw">
								<text class="left-con">单位:</text>
								<text>{{uFormModel.dw}}</text>
							</view>
							<view class="shopTishi-view-half" v-if="uFormModel.gg">
								<text class="left-con">规格:</text>
								<text>{{uFormModel.gg}}</text>
							</view>
						</view>
					</view>
					<u-form-item label="盘点数量" :labelWidth="74" prop="pdsl" v-show="doingindex>=1">
						<u-input placeholder="请输入盘点数量" type="number" v-model="uFormModel.pdsl" :focus="focusObj.numFocus">
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('num',false)"></uni-icons>
						<text class="inp-right-text" v-else></text>
					</u-form-item>
				</u-form>
			</view>
		</view>
		<u-button type="primary" class="my-primary-button sticky-bottom" text="保存" throttleTime="2000" v-show="ifpage&&contentShow" @tap="save">
		</u-button>
		
		<view class="box-content" v-show="!ifpage">
			<edit :title="editTitleObj" :tableData="tableData" :state="state" @editSave="editSave" ref="editDetail"></edit>
		</view>
		
		<ocrText :ocrObj="ocrObj" :size="sizeObj" v-show="ocrShow" @ocrBack="ocrShow=false" @ocrSave="ocrSave"></ocrText>

		<!-- 弹窗。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 -->
		<u-overlay :show="coverShow"></u-overlay>
		<u-toast ref="uToast"></u-toast>
		<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
			<scroll-view style="max-height: 60vh;" scroll-y="true">
				<view v-if="ifDrawer=='title'">
					<view class="list" v-for="(v, i) in selectData" @tap="titleChange(v.id,v.name)">
						<text>{{v.id}}</text>-
						<text>{{v.name}}</text>
					</view>
				</view>
				<view v-else-if="ifDrawer=='goods'" class="shop">
					<view class="list" v-for="(v, i) in selectData" @tap="setForm(v,false)">
						<view class="shop-l">{{i+1}}:</view>
						<view class="shop-r">
							<view class="shop-r-t">
								<text class="t-name">{{v.spmc}}</text>
								<text class="t-price">￥{{v.nsjg}}</text>
							</view>
							<view class="shop-r-b">
								<view>
									<text class="b-xi">编码:</text>
									<text class="b-cu">{{v.spbm}}</text>
								</view>
								<view>
									<text class="b-xi">条码:</text>
									<text class="b-cu">{{v.spsmm}}</text>
								</view>
							</view>
						</view>
					</view>
				</view>
				<view v-else-if="ifDrawer=='apiErrMess'" class="shop">
					<view class="list" v-for="(v, i) in selectData" @tap="apiErrMess(v,i)">
						<view class="shop-l">{{i+1}}:</view>
						<view class="shop-r">
							<view class="shop-r-t">
								<text class="t-name">{{v.message}}</text>
							</view>
							<view class="shop-r-b">
								<view>
									<text class="b-xi">编码:</text>
									<text class="b-cu">{{v.spbm}}</text>
								</view>
								<view>
									<text class="b-xi">条码:</text>
									<text class="b-cu">{{v.spsmm}}</text>
								</view>
							</view>
						</view>
					</view>
				</view>
			</scroll-view>
			<view class="closebtn" @tap="popupShow=false">取消</view>
		</u-popup>
		<movable-area class="movable-area" v-show="ifpage&&contentShow">
			<movable-view class="movable-view" :x="x" :y="y" direction="all" :animation="true" :out-of-bounds="false" v-show="!isVoiceMode">
				<view class="iconimgs guideJS2" @tap="changeDh">
					<view class="iconView">
						<uni-icons type="more-filled" size="30" color="#fff"></uni-icons>
					</view>
					<uni-transition mode-class="fade" class="uni-transition" ref="uniTransition" :show="true">
						<view class="my-fab-view" style="width:46px;"></view>
						<view class="my-fab-view" @tap="changeSwitch">
							<uni-icons custom-prefix="iconfont" type="icon-yuyin" color="#fff" size="30"></uni-icons>
							<view>语音</view>
						</view>
						<view class="my-fab-view" @tap="ocrShow=true">
							<uni-icons custom-prefix="iconfont" type="icon-OCRshibieguanli" color="#fff" size="30"></uni-icons>
							<view>表格识别</view>
						</view>
						<view class="my-fab-view" @tap="toAiset">
							<uni-icons type="gear-filled" color="#fff" size="34"></uni-icons>
							<view>设置</view>
						</view>
					</uni-transition>
				</view>
			</movable-view>
			<movable-view class="movable-view movable-view-large" :x="x" :y="y" direction="all" :animation="true" :out-of-bounds="false" v-show="isVoiceMode">
				<goodsVoice ref="goodsVoice" :step="yuyinModelArr" :list.sync="selectData" :searchCode="searchCode" @changeParentValue="changeParentValue" @changeVoiceShow="changeVoiceShow" @doing="doing"></goodsVoice>
			</movable-view>
		</movable-area>


	</view>
</template>

<script>
	import dayjs from "dayjs";
	import {
		QueryCk,
		Basic,
		PddCheck,
		PddDelete,
		Search,
		GetlistC,
		PddDosave,
		OrderNew,
		AiSetup
	} from "@/network/api.js";
	import xuanSwitch from "@/components/xuan-switch/xuan-switch.vue";
	import goodsVoice from '@/components/goodsVoice/goodsVoice';
	import edit from "./edit.vue";
	import ocrText from "@/components/ocr/ocr.vue";
	export default {
		components: {
			xuanSwitch,
			goodsVoice,
			edit,
			ocrText
		},
		data() {
			return {
				x: 400,
				y: 300,
				ifpage: true,
				uFormTitle: {
					pdlx: "",
					pdck: "",
					pdfd: `${uni.getStorageSync("fdbh")}-${uni.getStorageSync("fdmc")}`,
				},
				state: "",
				myCollShow: true,
				// 表单内容data
				neworderShow:false,
				uFormModel:{
					spbm: "",
					spsmm: "",
					spmc: "",
					dw: "",
					gg: "",
					pdsl: "",
				},
				uFormRules:{
					"spbm": {
						asyncValidator: (rule, value, callback) => {
							if(value.replace(/[^\x00-\xff]/g, "xx").length>=4){
								callback();
							}else{
								callback(new Error("请输入4位及以上的商品编码/名称/条码 查询"));
							}
						}
					},
					"pdsl": [{
							type: "number",
							required: true,
							message: "请填写盘点数量",
							trigger: ["blur", "change"]
						},
						{
							asyncValidator: (rule, value, callback) => {
								let reg=/^\d+(\.\d+)?$/
								if(reg.test(value)){
									callback();
								}else{
									callback(new Error('请输入非负数'));
								}
							}
						}
					]
				},
				
				contentShow:false,
				spbmClearShow:false,
				isSpComplete: false,
				
				ispda: uni.getStorageSync("pda"),
				focusObj:{
					spbmFocus:false,
					numFocus:false
				},
				selectId: "",
				uploadarr: [],
				serchGoodsData:"",
				// ai设置 data
				ocrObj:{},
				// 第二页面 已上传数据页面 data
				tableData:[],
				editTitleObj:{},
				// 弹窗data
				ifDrawer: "",
				selectData: [],// 搜索的数据(语音)
				popupShow: false,
				coverShow: false,
				//语音data
				isVoiceMode: false,//是否语音模式
				doingId: "",
				doingindex: 100,
				yuyinModelArr:[],
				searchCode: 400,
				yuyinArr:[{
						"tips": "请说出您要搜索的商品",//提示
						"id": "spbm",//需要填充的id
						"fixedId": "shoping",//固定值，部门-depart，分店-fendian，商家-business，商品-shoping，数量-num，价格-price，备注-remarks，开关-switch，保存-save
						"searchFunc": "serchGoods",//需要搜索的函数名
						"isSearchCon": true,//是否需要搜索内容
						"nextFunc": "setForm",//是否需要进行搜索 的函数名
						"completeFunc": "",//步骤完成的函数名
						"isSkip": false,//是否可以跳过该步骤，不需要输入
						"isNext": true,//该步骤完成后是否可以自动跳转到下一步
						"isShow": true,//是否显示
						"djlx": "SPPDB"
					},
					{
						"tips": "请说出您要输入的数量",
						"id": "pdsl",
						"fixedId": "num",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "",
						"isSkip": false,
						"isNext": true,
						"isShow": true,
						"djlx": "SPPDB"
					},
					{
						"tips": "请确认是否保存商品",
						"id": "",
						"fixedId": "save",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "save",
						"isSkip": false,
						"isNext": false,
						"isShow": true,
						"djlx": "SPPDB"
					}
				],
				// OCR
				ocrShow:false,
				sizeObj: {},
				aiDhShow:false
				
			}
		},
		onLoad(option) {
			uni.getSystemInfo({
				success: function(res) {
					this.x=70
					this.y=res.screenHeight-140
				}.bind(this)
			});
			this.uFormTitle.djbh = option.djbh
			this.state = option.state
			let ckVal=""
			if (option.state == "add") {
				this.editTitleObj=option
			}else if (option.state == "edit"||option.state == "look") {
				this.ifpage = true
				ckVal=option.pdck
				this.getList()
				this.editTitleObj=option
			}
			this.queryPdlx(true)
			this.queryCk(true, ckVal)
			// this.queryMore(true, "FDINFO", "pdfd")
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
			let dataes={
				"access_token": uni.getStorageSync("access_token"),
				"djtype": "SPBHD",
				"fdbh": uni.getStorageSync("fdbh"),
				"userid": uni.getStorageSync("userid"),
				"companyid": uni.getStorageSync("companyid")
			}
			AiSetup(dataes).then((res) => {
				console.log("#### AiSetup res",res)
				if(res.error_code==0){
					
				}else{
					this.$refs.uToast.show({
						type: "error",
						message: "查询单据设置失败"
					})
				}
			}).catch((err) => {
				console.log(err)
			})
		},
		onResize: function(e) {
			this.sizeObj=e.size
		},
		methods: {
			changeDh(){
				if(this.aiDhShow){
					this.$refs.uniTransition.step({
						opacity: 0,
					},{
						timingFunction: 'linear',
						duration: 50
					})
					this.$refs.uniTransition.step({
						width: "0px"
					},{
						timingFunction: 'linear',
						duration: 500
					})
				}else{
					this.$refs.uniTransition.step({
						opacity: 1,
					},{
						timingFunction: 'linear',
						duration: 0
					})
					this.$refs.uniTransition.step({
						width: "207px"
					},{
						timingFunction: 'linear',
						duration: 500
					})
				}
				this.$refs.uniTransition.run(()=>{// 开始执行动画
					this.aiDhShow=!this.aiDhShow
				})
			},
			
			// 语音模式。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。
			changeSwitch(){
				if(this.state=="look"||this.state=="check"){
					return
				}
				this.myCollShow=false
				this.contentShow=true
				this.yuyinModelArr=this.yuyinArr
				this.isVoiceMode=true
				uni.getSystemInfo({
					success: function(res) {
						this.x=0
						this.y=res.screenHeight-res.statusBarHeight-94
					}.bind(this)
				});
			},
			changeVoiceShow(){// 进入普通模式
				this.yuyinModelArr=[]
				this.isVoiceMode=false
				uni.getSystemInfo({
					success: function(res) {
						this.x=70
						this.y=res.screenHeight-140
					}.bind(this)
				});
			},
			changeParentValue(obj){
				if(obj.nextFunc){
					if(obj.searchFunc){
						this[obj.nextFunc](obj.val,true)
					}else{//为类似 switch开关时的 操作
						this[obj.nextFunc](obj.val)
					}
				}else{
					if(!obj.isSearchCon){
						this.uFormModel[obj.id]=obj.val
					}
				}
				if(obj.completeFunc){
					this[obj.completeFunc]()
				}
				if(obj.next){
					this.$refs.goodsVoice.stepFunc(obj.next)
				}
			},
			doing(id,index){
				this.doingId=id
				this.doingindex=index
			},
			clickYuyin(fixid,isnext){//语音 执行顺序跳转
				for(var i in this.yuyinModelArr){
					if(this.yuyinModelArr[i].fixedId==fixid){
						if(isnext){//从下一步开始顺序执行
							if(i!=this.yuyinModelArr.length-1){
								this.$refs.goodsVoice.stepFunc(Number(i)+1)
								return
							}
						}else{//从本步骤开始顺序执行
							// this.$set(this.yuyinModelArr[i], "isNext", false)
							this.$refs.goodsVoice.stepFunc(Number(i))
							return
						}
					}
				}
			},
			fillYuYinData(index,i,JSON_arr){
				this.$set(this.yuyinArr[index], "tips", JSON_arr[i].tips)
				this.$set(this.yuyinArr[index], "isSkip", JSON_arr[i].isskip)
				this.$set(this.yuyinArr[index], "isNext", JSON_arr[i].isnext)
				this.$set(this.yuyinArr[index], "isShow", JSON_arr[i].isshow)
			},
			
			toAiset(){
				uni.navigateTo({
					url: "/pages/my/component/aiset/aiset?djlx=SPPDB"
				});
				uni.$emit("aiUpdate",this.ocrObj)
			},
			//新增单据 单头 函数........................................................
			// 查询仓库
			queryPdlx(){
				let arr=[
					{
						"id":"HAND",
						"name":"手工录入"
					},
					{
						"id":"POS",
						"name":"前台盘点"
					},
					{
						"id":"HWB",
						"name":"货位盘点"
					},
					{
						"id":"PDJ",
						"name":"手持盘点机"
					},
					{
						"id":"DYPD",
						"name":"动态手工盘点"
					}
				]
				this.uFormTitle.pdlx = "PDJ-手持盘点机"
			},
			// 查询仓库
			queryCk(isauto,value){
				if (!isauto) {
					if (this.state == "pladd" || this.state == "edit" || this.state == "look" || this.state == "check") {
						return
					}
				}
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"fdbh": uni.getStorageSync("fdbh"),
				}
				QueryCk(dataes).then((res) => {
					console.log("QueryCk res",res)
					if(res.error_code==0){
						if(value){
							for (var i in res.data) {
								if(res.data[i].ckbmid==value){
									this.uFormTitle.pdck = `${res.data[i].ckbmid}-${res.data[i].ckmc}`
								}
							}
						}else{
							if (isauto) { //自动填充
								this.uFormTitle.pdck = `${res.data[0].ckbmid}-${res.data[0].ckmc}`
							}else{
								this.selectId = "pdck"
								this.selectData = []
								this.popupShow = true
								this.ifDrawer = "title"
								for (var i in res.data) {
									this.selectData.push({
										"id": res.data[i].ckbmid,
										"name": res.data[i].ckmc
									})
								}
							}
						}
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			// 新增单据 查询更多 查询更多
			queryMore(isauto, type, fixid) {
				if (!isauto) {
					if (this.state == "pladd" || this.state == "edit" || this.state == "look" || this.state == "check") {
						return
					}
				}
				if(type=="FDINFO"){
					if(uni.getStorageSync("fdlx")!="PS"){
						this.uFormTitle[fixid] = `${uni.getStorageSync("fdbh")}-${uni.getStorageSync("fdmc")}`
						return
					}
				}
				this.searchCode=400
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"dtype": type,
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					// console.log(type + " 基本信息basic res", res)
					if (res.error_code == 0) {
						if (isauto) { //自动填充
							if (type == "FDINFO") {
								this.uFormTitle[fixid] = `${res.data[0].fdbh}-${res.data[0].fdmc}`
							} else{
					
							}
						} else {
							this.selectId = fixid
							this.selectData = []
							this.popupShow = true
							this.ifDrawer = "title"
							for (var i in res.data) {
								if (type == "FDINFO") {
									this.selectData.push({
										"id": res.data[i].fdbh,
										"name": res.data[i].fdmc
									})
								} else{
					
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
			// 报审核
			subCheck() {
				uni.showModal({
					content: "单据审核后不可修改，是否继续",
					cancelText: "否",
					confirmText: "是",
					success: (resm)=> {
						if (resm.confirm) {
							let dataes = {
								"access_token": uni.getStorageSync("access_token"),
								"userid": uni.getStorageSync("userid"),
								"username": uni.getStorageSync("dlmc"),
								"djbh": this.uFormTitle.djbh,
								"fdbh": uni.getStorageSync("fdbh"),
								"pdlx": this.uFormTitle.pdlx.split("-")[0],
								"ckbmid": this.uFormTitle.pdck.split("-")[0],
								"pdmd": this.uFormTitle.pdfd.split("-")[0],
								"remark": ""
							}
							PddCheck(dataes).then((res) => {
								console.log("报审核 res", res)
								if (res.error_code == 0) {
									this.$refs.uToast.show({
										type: "success",
										message: "审核成功"
									})
									this.state = "check"
								} else {
									this.$refs.uToast.show({
										type: "error",
										message: res.message
									})
								}
							}).catch((err) => {
								console.log(err)
							})
						} else if (resm.cancel) {
							
						}
					}
				});
			},
			// 整单 删除
			deldh() {
				uni.showModal({
					content: "是否确认删除整个单号",
					success: (res)=> {
						if (res.confirm) {
							let dataes={
								"access_token": uni.getStorageSync("access_token"),
								"userid": uni.getStorageSync("userid"),
								"username": uni.getStorageSync("dlmc"),
								"djbh": this.uFormTitle.djbh
							}
							PddDelete(dataes).then((res) => {
								// console.log("删除单据 res", res)
								if (res.error_code == 0) {
									this.$refs.uToast.show({
										type: "success",
										message: "删除成功"
									})
									this.uFormTitle.djbh = ""
									setTimeout(()=>{
										uni.navigateBack({
											delta: 1
										});
									},1800)
								} else {
									this.$refs.uToast.show({
										type: "error",
										message: res.message
									})
								}
							}).catch((err) => {
								console.log(err)
							})
						} else if (res.cancel) {
							
						}
					}
				});
			},

			// 单据内容 函数...............................................................
			// 搜索商品编码
			spbmChange(val) {
				if (this.isSpComplete) {
					return
				}
				setTimeout(() => {
					if (val != "") {
						if (val == this.uFormModel.spbm) {
							this.$refs.uForm.validateField("spbm",(error)=>{
								if(error.length==0){
									this.serchGoods(val)
									uni.hideKeyboard()
								}
							})
						}
					}
				}, 2000)
			},
			serchGoods(val) {
				this.coverShow = true
				uni.showLoading({
					title: "搜索中"
				});
				this.searchCode=400
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"companyid": uni.getStorageSync("companyid"),
					"condition": val,
					"fdbh": uni.getStorageSync("fdbh"),
					"findtype": "01",
					"goodstype": "SP",
					"userid": uni.getStorageSync("userid"),
				}
				Search(dataes).then((res) => {
					uni.hideLoading()
					this.coverShow = false
					console.log("查找商品 res", res)
					if (res.error_code == 0) {
						this.uploadarr = []
						if (res.data.length == 1) {
							this.setForm(res.data[0],true)
						} else {
							this.ifDrawer = "goods"
							this.popupShow = true
							this.selectData = res.data.slice(0, 20)
							this.searchCode=0
						}
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.message
						})
					}
				}).catch((err) => {
					uni.hideLoading()
					console.log(err)
				})
			},
			setForm(data,isauto){
				this.serchGoodsData = data
				this.uFormModel.spbm=data.spbm
				this.uFormModel.spsmm=data.spsmm
				this.uFormModel.spmc=data.spmc
				this.uFormModel.dw=data.dw
				this.uFormModel.gg=data.gg
				this.uFormModel.pdsl=""
				this.popupShow=false
				this.searchCode=400
				this.isSpComplete = true
				// console.log("this.serchGoodsData this.serchGoodsData", this.serchGoodsData)
				if(this.isVoiceMode){
					let arrTemp=[]
					arrTemp.push(data)
					this.selectData = arrTemp
					this.searchCode=0
					if(!isauto){//语音模式 手动点击调用函数时
						this.clickYuyin("shoping",true)
					}
				}else{
					setTimeout(()=>{
						this.focusObj.numFocus=true
					},300)
				}
			},
			// 扫码 搜索商品
			scan() {
				uni.scanCode({
					success: (res) => {
						console.log('扫码内容', res)
						this.uFormModel.spbm=res.result
						this.serchGoods(this.uFormModel.spbm)
					},
					fail: (err) => {
						this.$refs.uToast.show({
							type:"error",
							message: "识别失败"
						})
					}
				});
			}, 
			
			//获取该单号已上传的商品
			getList() {
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djbh": this.uFormTitle.djbh,
					"djtype": "SPPDB",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"ztbz": "T"
				}
				GetlistC(dataes).then((res) => {
					console.log("获取该单号已上传的商品 res", res)
					if (res.error_code == 0) {
						this.tableData = []
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
			//查找表格列(新增)。。。
			getcolumns() {
				this.myCollShow=false
				this.contentShow=true
				setTimeout(()=>{
					this.focusObj.spbmFocus=true
				},300)
			},
			
			clearAlone(item) {
				if(this.isVoiceMode){
					
				}else{
					this.uFormModel[item]=""
				}
			},
			clearForm() {
				this.uFormModel.spbm= ""
				this.uFormModel.spsmm= ""
				this.uFormModel.spmc= ""
				this.uFormModel.dw= ""
				this.uFormModel.gg= ""
				this.uFormModel.pdsl= ""
			},
			clearFocus() {
				this.focusObj.spbmFocus=false
				this.focusObj.numFocus=false
				this.focusObj.priceFocus=false
				this.focusObj.remarksFocus=false
			},
			
			// 编辑商品 保存商品............................................................
			editSave(arr) {
				this.uploadarr = arr
				this.doSave("EDIT",false)
			},
			toback(){
				this.ifpage=true
				this.getList()
			},
			
			newOrder() {
				if (this.state == "add") {
					return
				}
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djtype": "SPPDB",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
				}
				OrderNew(dataes).then((res) => {
					if (res.error_code == 0) {
						this.uFormTitle.djbh = res.djbh
						this.state = "add"
						this.queryPdlx(true)
						this.queryCk(true,"")
						// this.queryMore(true, "FDINFO", "pdfd")
						this.tableData = []
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

			// popup弹窗控制...................................................................
			// 表头弹窗
			titleChange(id, name) {
				if (this.selectId) {
					this.uFormTitle[this.selectId] = `${id}-${name}`
				}
				this.popupShow = false
				this.selectId = ""
			},
			
			ocrSave(arr) {
				// this.uploadarr = arr
				// for(var i in this.uploadarr){
				// 	this.$set(this.uploadarr[i], "guid", "")
				// 	this.$set(this.uploadarr[i], "scrq", dayjs().format("YYYY-MM-DD"))
				// }
				// console.log("OCR 保存数据 this.uploadarr",this.uploadarr)
				// this.doSave("CHK",false)
			},
			// 保存事件函数。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。
			save() {
				this.$refs.uForm.validate().then(resf => {
					this.uploadarr = []
					this.uploadarr.push({
						"spbm": this.uFormModel.spbm,
						"spsmm": this.uFormModel.spsmm,
						"spmc": this.uFormModel.spmc,
						"jcl": this.serchGoodsData.zdkcl,
						"spsl": this.uFormModel.pdsl,
						"nsjg": this.serchGoodsData.nsjg,
						"cbjg": this.serchGoodsData.hyjg,
						"guid": ""
					})
					this.doSave("CHK",false)
				}).catch(errors => {
					
				})
			},
			doSave(state,isOcr) {
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"userid": uni.getStorageSync("userid"),
					"vtype": state,
					"djbh": this.uFormTitle.djbh,
					"fdbh": uni.getStorageSync("fdbh"),
					"ck": this.uFormTitle.pdck.split("-")[0],
					"pdmd": this.uFormTitle.pdfd.split("-")[0],
					"pdlx": this.uFormTitle.pdlx.split("-")[0],
					"remark": "",
					"list": this.uploadarr
				}
				console.log("state=="+state+"; 保存商品 dosave dataes", dataes)
				PddDosave(dataes).then((res) => {
					console.log("state=="+state+"; 保存商品 dosave res", res)
					if (state == "EDIT") {
						if (res.error_code == 0) {
							this.$refs.uToast.show({
								type: "success",
								message: "保存成功"
							})
							this.$refs.editDetail.stateDetail = false
							this.$refs.editDetail.tableIndex = -1
							this.getList()
						}
						return
					}
					
					if(this.uploadarr.length>1){//多条数据
						if(res.error_data){
							if(res.error_data.length>1){
								this.ifDrawer="apiErrMess"
								this.selectData=res.error_data
								this.popupShow=true
								return
							}
						}
					}
					
					if (res.error_code == 0) {
						this.$refs.uToast.show({
							type: "success",
							message: "保存成功"
						})
						this.state = "pladd"
						this.uploadarr = []
						this.clearFocus()
						this.clearForm()
						this.getList()
						if(this.isVoiceMode){//语音模式时
							this.yuyinModelArr=[]
							setTimeout(()=>{
								this.yuyinModelArr=this.yuyinArr
							},1500)
						}else{
							if(!isOcr){
								setTimeout(()=>{
									this.focusObj.spbmFocus=true
								},300)
							}
						}
					} else if (res.error_code == 2) {
						if(res.error_data[0].submit){
							uni.showModal({
								content: res.error_data[0].message,
								success: (res)=> {
									if (res.confirm) {
										this.doSave("ADD",false)
									} else if (res.cancel) {
									
									}
								}
							});
						}else{
							uni.showModal({
								title: "应用提示",
								content: res.error_data[0].message,
								showCancel: false,
								success: (res)=> {
									if (res.confirm) {
										
									}
								}
							});
						}
					} else if (res.error_code == 500) {
						this.$refs.uToast.show({
							type: "error",
							message: "保存失败"
						})
					} else {
						this.$refs.uToast.show({
							type: "error",
							message: res.error_data[0].message
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			apiErrMess(item,index){
				if(item.submit){
					uni.showModal({
						content: item.message,
						success: (res)=> {
							if (res.confirm) {
								this.uploadarr=[]
								delete item.submit
								delete item.message
								this.uploadarr.push(item)
								this.doSave("ADD",true)
								this.selectData.splice(index,1)
							} else if (res.cancel) {
							
							}
						}
					});
				}
			}
			
			
		},
		computed: {
			skin() {
				return this.$store.state.skin;
			}
		},
		watch: {
			state: function(newv, oldv) {
				if(newv == "add"){
					this.neworderShow=true
					setTimeout(()=>{
						this.neworderShow=false
					},4000)
				}else{
					this.neworderShow=false
				}
			},
			"uFormModel.spbm": function(newv, oldv) {
				if(newv.length==0){
					this.spbmClearShow=false
					this.isSpComplete=false
					this.clearForm()
					this.clearFocus()
				}else{
					
				}
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
		}

		.box-content {
			padding-top: calc(var(--status-bar-height) + 52px);
			padding-bottom: 0;

			.my-collapse {
				.my-collapse-title {
					padding:10px 10px 0;
					color: #fff;
					border-top-left-radius: 10px;
					border-top-right-radius: 10px;
					background-color: #358CC9;

					.fold-title {
						.fold-title-t {
							font-size: 15px;
							font-weight: 600;

							.dhliang {
								display: block;
								height: 17px;
								line-height: 16px;
								padding: 0px 12px;
								background-color: #F13B44;
								border-radius: 15px;
								font-size: 12px;
								font-weight: 500;
							}
						}

						.fold-title-con {
							font-size: 12px;
							font-weight: 400;
						}

						.fold-title-flex {
							display: flex;
							justify-content: space-between;
							align-items: center;
						}
					}
				}

				.my-collapse-con {
					background-color: #fff;
					padding-bottom: 10px;
					margin-bottom: 10px;

					.form-card {
						border-top-left-radius: 0;
						border-top-right-radius: 0;
					}
				}
			}

			.view-flex {
				width: 100%;
				display: flex;
				align-items: center;

				.form-left-text {
					display: inline-block;
					min-width: 64px;
					text-align-last: justify;
					margin-right: 10px;
				}
			}

			.form-card {
				padding: 10px 15px;
				background-color: #fff;
				border-bottom-left-radius: 10px;
				border-bottom-right-radius: 10px;
				margin-bottom: 10px;
				box-sizing: border-box;
				.shopTishi{
					width: 100%;
					display: flex;
					justify-content: space-between;
					align-items: center;
					.shopTishi-view{
						uni-text{
							font-size: 14px;
							color: #999;
							font-weight: 400;
						}
					}
					.shopTishi-view-half{
						width: 50%;
						uni-text{
							font-size: 14px;
							color: #999;
							font-weight: 400;
						}
					}
					.left-con{
						margin-right: 4px;
					}
					.show-dots{
						overflow: hidden;
						text-overflow: ellipsis;
						display: -webkit-box;
						-webkit-line-clamp: 2;
						-webkit-box-orient: vertical;
					}
				}
				.inp-right-text{
					display: inline-block;
					width: 19px;
				}
				.radio-view{
					display: flex;
					justify-content: space-between;
					align-items: center;
					.radio-text{
						padding: 6px 14px;
						background-color: #F5F5F5;
						border-radius: 7px;
					}
				}
				.uni-date {
					width: 100%;
					.uni-date__x-input {
						height: 36px;
					}
					.uni-date-editor--x .uni-date__icon-clear {
						top: 6px;
						border: 2px solid transparent;
					}
					.uni-date-editor--x__disabled>.uni-date-x {
						background-color: #F5F7FA;
					}
				}
				.u-input__content__subfix-icon {
					flex-direction: row;
				}
			}

			.form-card>* {
				margin-bottom: 14px;
			}

			.u-form.form-card>* {
				margin-bottom: 14px;
			}

			.form-card>*:last-child {
				margin-bottom: 0px;
			}

			/deep/.u-form {
				.u-form-item__body {
					padding: 0;
				}
				.u-form-item__body__right__content__slot{
					display: flex;
					justify-content: space-between;
				}
				.shoping{
					margin-bottom: 0px;
				}
			}

			.lxactive{
				background-color: #358CC9 !important;
				color: #fff;
			}
		}

		.flex-btns {
			display: flex;
			justify-content: space-between;

			/deep/.u-button--primary.my-primary-button {
				width: 28%;
			}
		}
		
		.movable-area {
			width: 100%;
			height: 100vh;
			position: fixed !important;
			top: 0;
			left: 0;
			z-index: 5001;
			pointer-events:none;
			
			.movable-view {
				height: 56px;
				display: flex;
				justify-content: flex-end;
				align-items: center;
				pointer-events:auto;
			}
			
			.movable-view-large{
				width: 100%;
				height: 206px;
				padding:0 10px;
				box-sizing: border-box;
			}
			
			.iconimgs{
				position: relative;
				z-index: 4500;
				
				.uni-transition{
					display: flex;
					align-items: center;
					color: #fff;
					width: 207px;
					overflow-x: hidden;
					height: 52px;
					font-size: 12px;
					padding-top: 6px;
					padding-bottom: 6px;
					border-radius: 30px;
					background-image: linear-gradient(to right, #48aeed, #3489c7);
					box-sizing: border-box;
					position: absolute;
					top: 0;
					left: 10px;
					z-index: -1;
					.my-fab-view{
						width: 50px;
						text-align: center;
					}
				}
				
				.iconView {
					width: 52px;
					height: 52px;
					display: flex;
					justify-content: center;
					align-items: center;
					border-radius: 50%;
					background-image: linear-gradient(to right, #48aeed, #3489c7);
					box-shadow: 0px 0px 6px 4px #cdcbcb;
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
</style>
<style lang="scss">
	page {
		background-color: #f8f8f8;
	}
</style>