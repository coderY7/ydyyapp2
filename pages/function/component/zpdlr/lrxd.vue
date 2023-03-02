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
			<view class="center" v-show="ifpage">直配录入新单</view>
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
							<text>单号:{{uFormTitle.dh}}</text>
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
						<u-form-item label="供货商家:" :labelWidth="90" prop="detail_sj" @tap="queryMore('SJINFO','detail_sj',false)">
							<u-input placeholder="请输入查询供货商家" v-model="uFormTitle.detail_sj" :disabled="state!=''" :disabledColor="state!='add'?'#F5F7FA':'#fff'">
							</u-input>
						</u-form-item>
						<u-form-item label="直配分店:" :labelWidth="90" prop="detail_fd" @tap="queryMore('FDINFO','detail_fd',false)">
							<u-input placeholder="请选择直配分店" v-model="uFormTitle.detail_fd"
								:disabled="state!=''" :disabledColor="state!='add'?'#F5F7FA':'#fff'">
							</u-input>
						</u-form-item>
						<u-form-item label="到货日期:" :labelWidth="90" prop="detail_ddrq">
							<uni-datetime-picker type="date" :border="true" ref="datetimePicker" :start="uFormTitle.startDate" :end="uFormTitle.endDate" v-model="uFormTitle.detail_ddrq" :disabled="state!='add'" @change="timeTChange"/>
						</u-form-item>
						<u-form-item label="备注说明:" :labelWidth="90" prop="detail_remark">
							<u-input placeholder="备注说明" v-model="uFormTitle.detail_remark">
							</u-input>
						</u-form-item>
					</u-form>
					<view class="flex-btns">
						<u-button type="primary" class="my-primary-button" text="审核" throttleTime="1000"
							:disabled="state=='add'||state=='check'||state=='look'"
							@tap="auditOrder"></u-button>
						<u-button type="primary" class="my-primary-button" text="删单" throttleTime="1000"
							:disabled="state=='add'||state=='check'||state=='look'"
							@tap="delOrder"></u-button>
						<u-button type="primary" class="my-primary-button" text="添加商品" throttleTime="1000"
							:disabled="option.title!='未审核'&&state=='look'||state=='check'"
							@tap="addGoods"></u-button>
					</view>
				</view>
			</view>
			<!-- 新增单据 内容 -->
			<view v-show="contentShow">
				<u-form ref="uForm" :model="uFormModel" :rules="uFormRules" class="form-card">
					<u-form-item label="商品编码:" :labelWidth="90" prop="goods_spbm" class="shoping">
						<u-input placeholder="请至少输入四位条码" v-model="uFormModel.goods_spbm"
							:disabled="state=='edit'" :focus="focusObj.spbmFocus" @change="spbmChange">
							<template slot="suffix">
								<uni-icons type="clear" size="19" color="#e1e1e1" v-if="uFormModel.goods_spbm!=''"
									@tap="clearAlone('goods_spbm')"></uni-icons>
							</template>
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin"
							:color="doingId=='shoping'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"
							@tap="clickYuyin('shoping',false)"></uni-icons>
						<uni-icons custom-prefix="iconfont" type="icon-shouye" size="20" color="#606060" @tap="scan()"
							v-if="!isVoiceMode&&!ispda"></uni-icons>
					</u-form-item>
					<view>
						<view class="shopTishi">
							<view class="shopTishi-view show-dots" v-if="uFormModel.goods_spmc">
								<text class="left-con">名称-条码:</text>
								<text>{{uFormModel.goods_spmc}}</text>-
								<text>{{uFormModel.goods_spsmm}}</text>
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
						<view class="shopTishi">
							<view class="shopTishi-view-half" v-if="uFormModel.jjje">
								<text class="left-con">进价金额:</text>
								<text>{{uFormModel.jjje}}</text>
							</view>
							<view class="shopTishi-view-half" v-if="uFormModel.dqkcl">
								<text class="left-con">当前库存:</text>
								<text>{{uFormModel.dqkcl}}</text>
							</view>
						</view>
						<view class="shopTishi">
							<view class="shopTishi-view-half" v-if="uFormModel.wlbzs">
								<text class="left-con">物流包装数:</text>
								<text>{{uFormModel.wlbzs}}</text>
							</view>
							<view class="shopTishi-view-half" v-if="uFormModel.bzrq">
								<text class="left-con">保质日期:</text>
								<text>{{uFormModel.bzrq}}</text>
							</view>
						</view>
					</view>
					<u-form-item label="直配数量:" :labelWidth="90" prop="goods_zpsl">
						<u-input placeholder="请输入直配数量" type="number" v-model="uFormModel.goods_zpsl" :focus="focusObj.numFocus">
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('num',false)"></uni-icons>
						<text class="inp-right-text" v-else></text>
					</u-form-item>
					<u-form-item label="直配进价:" :labelWidth="90" prop="goods_zpjj">
						<u-input placeholder="请输入直配进价" type="number" v-model="uFormModel.goods_zpjj" :focus="focusObj.priceFocus">
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='price'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('price',false)"></uni-icons>
						<text class="inp-right-text" v-else></text>
					</u-form-item>
					<u-form-item label="商品备注:" :labelWidth="90" prop="goods_remark">
						<u-input placeholder="商品备注" v-model="uFormModel.goods_remark">
						</u-input>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='price'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('remarks',false)"></uni-icons>
						<text class="inp-right-text" v-else></text>
					</u-form-item>
				</u-form>
				<view class="form-card">
					<view style="display:flex;justify-content:space-between;">
						<text>特供类型</text>
						<uni-icons custom-prefix="iconfont" type="icon-yuyin" :color="doingId=='lx'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode" @tap="clickYuyin('lx',false)"></uni-icons>
					</view>
					<view>
						<view class="radio-view">
							<view class="radio-text" v-for="(v, i) in lxlist" :class="{lxactive:uFormModel.cxtype==v.sjcxlxid}" @tap="formMoreChange(v.sjcxlxid+'-'+v.lxmc,false)">{{v.lxmc}}</view>
						</view>
					</view>
				</view>
				
			</view>
		</view>
		<u-button type="primary" class="my-primary-button sticky-bottom" text="保存" throttleTime="2000"
			v-show="ifpage&&contentShow" @tap="save">
		</u-button>
		
		<view class="box-content" v-show="!ifpage">
			<edit :tobj="editTitleObj" :tableData="tableData" :state="state" @editSave="editSave" ref="editDetail"></edit>
		</view>
		
		<ocrText :ocrObj="ocrObj" :size="sizeObj" v-show="ocrShow" @ocrBack="ocrShow=false" @ocrSave="ocrSave"></ocrText>
		
		<!-- 弹窗。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 -->
		<u-popup :show="popupShow" mode="center" class="u-popup-center" zIndex="5000">
			<scroll-view style="max-height: 60vh;" scroll-y="true">
				<view v-if="ifDrawer=='detail_sj'">
					<view class="list" v-for="(v, i) in selectData" @tap="titleChange(ifDrawer,v.id+'-'+v.name)">
						<text>{{v.id}}</text>-
						<text>{{v.name}}</text>
					</view>
				</view>
				<view v-else-if="ifDrawer=='detail_fd'">
					<view class="list" v-for="(v, i) in selectData" @tap="titleChange(ifDrawer,v.id+'-'+v.name)">
						<text>{{v.id}}</text>-
						<text>{{v.name}}</text>
					</view>
				</view>
				<view v-else-if="ifDrawer=='goods_spbm'" class="shop">
					<view class="list" v-for="(v, i) in selectData" @tap="setForm(v,false)">
						<view class="shop-l">{{i+1}}:</view>
						<view class="shop-r">
							<view class="shop-r-t">
								<text class="t-name">{{v.spmc}}</text>
								<text class="t-price">￥{{v.pjjj}}</text>
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
		<u-toast ref="uToast"></u-toast>
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
	import dayjs from 'dayjs';
	import {
		GetlistC,
		Basic,
		OrderNew,
		Search,
		ZpdCheck,
		ZpdDelete,
		ZpdDosave,
		ZpdSjghjg,
		ZpdKcinfo,
		AiSetup
	} from "@/network/api.js";
	import goodsVoice from '@/components/goodsVoice/goodsVoice';
	import edit from "./edit.vue";
	import ocrText from "@/components/ocr/ocr.vue";
	export default {
		components: {
			goodsVoice,
			edit,
			ocrText
		},
		data() {
			return {
				x: 400,
				y: 300,
				option: "",
				ifpage: true,
				state: "",
				myCollShow: true,
				uFormTitle:{
					detail_sj: "",
					detail_fd: uni.getStorageSync("fdbh") + "-" + uni.getStorageSync("fdmc"),
					isimportjg: false,
					detail_ddrq: dayjs().format('YYYY-MM-DD'),
					startDate: "2020-01-01",
					endDate: "2025-12-31",
					detail_remark: ""
				},
				contentShow: false,
				isSpComplete: false,
				// 
				neworderShow: false,
				uFormModel: {
					goods_spbm: "",
					goods_spsmm: "",
					goods_spmc: "",
					dw: "",
					gg: "",
					goods_zpsl: "",
					goods_zpjj: "",
					dqkcl: "",
					nsjg: "",
					jjje: "",
					hsjj: 0,
					cxtype: "",
					wlbzs: "",
					bzrq: "",
					bzqts: "",
					goods_remark: "",
				},
				uFormRules: {
					"goods_spbm": {
						asyncValidator: (rule, value, callback) => {
							if(value.replace(/[^\x00-\xff]/g, "xx").length>=4){
								callback();
							}else{
								callback(new Error("请输入4位及以上的商品编码/名称/条码 查询"));
							}
						}
					}
				},
				uploadarr: [],
				lxlist:[],
				ispda: uni.getStorageSync("pda"),
				focusObj: {
					spbmFocus: false,
					numFocus: false,
					priceFocus: false,
				},
				// ai设置 data
				ocrObj:{},
				// 第二页面 已上传数据页面 data
				tableData:[],
				editTitleObj:{},
				// 弹窗data
				ifDrawer: "",
				selectData: [],// 搜索的数据(语音)
				popupShow: false,
				// 语音data
				isVoiceMode: false,//是否语音模式
				doingId: "",
				doingindex: 100,
				yuyinModelArr:[],
				searchCode: 400,
				yuyinArr:[{
						"tips": "请说出您要搜索的商品",//提示
						"id": "goods_spbm",//需要填充的id
						"fixedId": "shoping",//固定值，部门-depart，分店-fendian，商家-business，商品-shoping，数量-num，价格-price，备注-remarks，开关-switch，保存-save
						"searchFunc": "searchSpbm",//需要搜索的函数名
						"isSearchCon": true,//是否需要搜索内容
						"nextFunc": "radioChange",//是否需要进行搜索 的函数名
						"completeFunc": "",//步骤完成的函数名
						"isSkip": false,//是否可以跳过该步骤，不需要输入
						"isNext": true,//该步骤完成后是否可以自动跳转到下一步
						"isShow": true,//是否显示
						"djlx": "ZPDBH"
					},
					{
						"tips": "请说出您要输入的直配数量",
						"id": "goods_zpsl",
						"fixedId": "num",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "",
						"isSkip": true,
						"isNext": true,
						"isShow": true,
						"djlx": "ZPDBH"
					},
					{
						"tips": "请说出您要输入的直配进价",
						"id": "goods_zpjj",
						"fixedId": "price",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "",
						"isSkip": true,
						"isNext": true,
						"isShow": true,
						"djlx": "ZPDBH"
					},
					{
						"tips": "请说出您要输入的备注",
						"id": "goods_remark",
						"fixedId": "remarks",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "",
						"isSkip": true,
						"isNext": true,
						"isShow": true,
						"djlx": "ZPDBH"
					},
					{
						"tips": "请说出您要选择的供价类型",
						"id": "cxtype",
						"fixedId": "lx",
						"searchFunc": "formMore",
						"isSearchCon": true,
						"nextFunc": "formMoreChange",
						"completeFunc": "",
						"isSkip": true,
						"isNext": true,
						"isShow": true,
						"djlx": "ZPDBH"
					}, 
					{
						"tips": "请确认是否保存商品",
						"id": "",
						"fixedId": "save",
						"searchFunc": "",
						"isSearchCon": false,
						"nextFunc": "",
						"completeFunc": "saveGoods",
						"isSkip": false,
						"isNext": false,
						"isShow": true,
						"djlx": "ZPDBH"
					}
				],
				// OCR
				ocrShow:false,
				sizeObj: {},
				aiDhShow:false
				
			}
		},
		onLoad: function(option) {
			uni.getSystemInfo({
				success: function(res) {
					this.x=70
					this.y=res.screenHeight-140
				}.bind(this)
			});
			this.option = option
			this.uFormTitle.dh = option.dh
			this.state = option.orderType
			console.log("录入新单 option", option)
			if (option.orderType == "add") {
				this.queryMore("SJINFO", "detail_sj", true)
				this.editTitleObj=option
			}else if (option.orderType == "edit"||option.orderType == "look") {
				this.ifpage = true
				this.uFormTitle.detail_sj=`${option.sjbh}-${option.sjmc}`
				this.uFormTitle.detail_fd=`${option.fdbh}-${option.fdmc}`
				this.getList()
				this.editTitleObj=option
			}
			this.formMore("",true)
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
				"djtype": "ZPDBH",
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
			// OCR表格识别............................................................
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
			changeSwitch() {
				this.yuyinModelArr = this.yuyinArr
				this.isVoiceMode = true
				uni.getSystemInfo({
					success: function(res) {
						this.x = 0
						this.y = res.screenHeight - res.statusBarHeight - 94
					}.bind(this)
				});
			},
			changeVoiceShow() { // 进入普通模式
				this.yuyinModelArr = []
				this.isVoiceMode = false
				uni.getSystemInfo({
					success: function(res) {
						this.x = res.screenWidth - 10
						this.y = res.screenHeight - 400
					}.bind(this)
				});
			},
			changeParentValue(obj) {
				if (obj.nextFunc) {
					if (obj.searchFunc) {
						this[obj.nextFunc](obj.val, true)
					} else { //为类似 switch开关时的 操作
						this[obj.nextFunc](obj.val)
					}
				} else {
					if (!obj.isSearchCon) {
						this.uFormModel[obj.id] = obj.val
					}
				}
				if (obj.completeFunc) {
					this[obj.completeFunc]()
				}
				if (obj.next) {
					this.$refs.goodsVoice.stepFunc(obj.next)
				}
			},
			doing(id, index) {
				this.doingId = id
				this.doingindex = index
			},
			clickYuyin(id, isnext) { //语音 执行顺序跳转
				for (var i in this.yuyinModelArr) {
					if (this.yuyinModelArr[i].id == id) {
						if (isnext) { //从下一步开始顺序执行
							if (i != this.yuyinModelArr.length - 1) {
								this.$refs.goodsVoice.stepFunc(Number(i) + 1)
								return
							}
						} else { //从本步骤开始顺序执行
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
					url: "/pages/my/component/aiset/aiset?djlx=ZPDBH"
				});
				uni.$emit("aiUpdate",this.ocrObj)
			},
			//获取该单号已上传的商品
			getList() {
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"djbh": this.uFormTitle.dh,
					"djtype": "ZPDBH",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
					"ztbz": "T"
				}
				GetlistC(dataes).then((res) => {
					console.log("获取该单号已上传的商品 res", res)
					if (res.error_code == 0) {
						if(res.data.length>0){
							this.uFormTitle.detail_ddrq=res.data[0].zprq.split("T")[0]
							this.uFormTitle.detail_remark=res.data[0].remark
						}
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
			
			// 搜索分店和商家
			queryMore(type, fixid, bol) {
				if (!bol) {
					if (this.state == "edit" || this.state == "pladd"|| this.state == "check") {
						if (fixid == "detail_fd"||fixid == "detail_sj") {
							return
						}
					}
				}
				this.ifDrawer = fixid
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"dtype": type,
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					console.log(type+"——基本信息basic res", res)
					if (res.error_code == 0) {
						if (bol) { //自动填充
							if (type == "FDINFO") {
								this.uFormTitle[fixid] = `${res.data[0].fdbh}-${res.data[0].fdmc}`
							} else if (type == "SJINFO") {
								this.uFormTitle[fixid] = `${res.data[0].sjbh}-${res.data[0].sjmc}`
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
							} else if (type == "SJINFO") {
								for (var i in res.data) {
									this.selectData.push({
										"id": res.data[i].sjbh,
										"name": res.data[i].sjmc
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
			timeTChange(event) {
				this.uFormTitle.detail_ddrq=event
			},
			// 新增商品
			addGoods(){
				if (this.option.title == "已审核") {
					this.$refs.uToast.show({
						type: "error",
						message: "该单据已审核，无法添加商品"
					})
					return
				}
				this.myCollShow = false
				this.contentShow = true
				setTimeout(() => {
					this.focusObj.spbmFocus = true
				}, 300)
			},
			// 审核整单
			auditOrder(){
				if (this.option.title == '已审核'){
					this.$refs.uToast.show({
						type:"error",
						message: "该单据已审核，无法继续审核"
					})
					return
				}
				uni.showModal({
					content: "审核后无法恢复，是否确定审核？",
					success: (res)=> {
						if (res.confirm) {
							let dataes = {
								"access_token": uni.getStorageSync("access_token"),
								"djbh": this.uFormTitle.dh,
								"fdbh": uni.getStorageSync("fdbh"),
								"userid": uni.getStorageSync("userid"),
								"username": uni.getStorageSync("dlmc"),
								"ydhrq": this.uFormTitle.detail_ddrq,
								"remark": this.uFormTitle.detail_remark,
							}
							console.log("审核商品 dataes", dataes)
							ZpdCheck(dataes).then((res) => {
								console.log("审核商品 res", res)
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
						} else if (res.cancel) {
							
						}
					}
				});
			},
			// 删除整单
			delOrder(){
				if (this.option.title == '已审核'){
					this.$refs.uToast.show({
						type:"error",
						message: "该单据已审核，无法删单"
					})
					return
				}
				uni.showModal({
					content: "删除后无法恢复，是否确定删除？",
					success: (res)=> {
						if (res.confirm) {
							let dataes = {
								"access_token": uni.getStorageSync("access_token"),
								"djbh": this.uFormTitle.dh,
								"userid": uni.getStorageSync("userid"),
								"username": uni.getStorageSync("dlmc")
							}
							// console.log("删除整单 dataes",dataes)
							ZpdDelete(dataes).then((res) => {
								console.log("删除整单 res", res)
								if (res.error_code == 0) {
									this.$refs.uToast.show({
										type: "success",
										message: "审核成功"
									})
									this.uFormTitle.dh = ""
									this.tableData = []
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
						} else if (res.cancel) {
							
						}
					}
				});
			},
			
			// 搜索商品编码
			spbmChange(val) {
				if (this.isSpComplete) {
					return
				}
				setTimeout(() => {
					if (val != "") {
						if (val == this.uFormModel.goods_spbm) {
							this.$refs.uForm.validateField("goods_spbm", (error) => {
								if (error.length == 0) {
									this.searchSpbm(val)
									uni.hideKeyboard()
								}
							})
						}
					}
				}, 2000)
			},
			searchSpbm(val) {
				this.searchCode = 400
				uni.showLoading({
					title: '搜索中，请稍后'
				});
				let conditions = val
				if (val == "") {
					conditions = "S=" + this.uFormTitle.sjbh
				}
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"companyid": uni.getStorageSync("companyid"),
					"condition": conditions,
					"fdbh": uni.getStorageSync("fdbh"),
					"findtype": "01",
					"goodstype": "SP",
					"userid": uni.getStorageSync("userid"),
				}
				Search(dataes).then((res) => {
					uni.hideLoading()
					console.log("查找商品 res", res)
					if (res.error_code == 0) {
						this.uploadarr = []
						if (res.data.length == 1) {
							this.setForm(res.data[0], true)
						} else {
							this.ifDrawer = "goods_spbm"
							this.popupShow = true
							this.selectData = res.data.slice(0, 20)
							this.searchCode = 0
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
			setForm(data, isauto) {
				this.searchCode = 400
				this.goodsinfo = data
				this.uFormModel.goods_spbm = data.spbm
				this.uFormModel.goods_spsmm = data.spsmm
				this.uFormModel.goods_spmc = data.spmc
				this.uFormModel.goods_zpsl = 1
				this.uFormModel.nsjg = data.nsjg
				this.uFormModel.hsjj = data.hyjg
				this.uFormModel.dw = data.dw
				this.uFormModel.gg = data.gg
				this.uFormModel.wlbzs = data.wlbzs
				this.uFormModel.bzrq = dayjs().date(dayjs().date() + data.bzqts).format("YYYY-MM-DD")
				this.uFormModel.bzqts = data.bzqts
				this.uFormModel.goods_remark = data.spremark
				this.searchSpkc(data.spbm)
				this.searchJgLx(data.spbm)
				this.popupShow = false
				this.isSpComplete = true
				// console.log("this.uFormModel", this.uFormModel)
				if (this.isVoiceMode) {
					let arrTemp = []
					arrTemp.push(data)
					this.selectData = arrTemp
					this.searchCode = 0
					if (!isauto) { //语音模式 手动点击调用函数时
						this.clickYuyin("shoping", true)
					}
				} else {
					setTimeout(()=>{
						this.focusObj.numFocus=true
					},300)
				}
			},
			searchSpkc(spbm){
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"fdbh": uni.getStorageSync("fdbh"),
					"spbm": spbm
				}
				ZpdKcinfo(dataes).then((res) => {
					console.log("查找商品库存信息 res", res)
					if (res.error_code == 0) {
						this.uFormModel.dqkcl=res.data_info.dqkcl
					} else {
						
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			searchJgLx(spbm){
				let dataes = {
					"access_token": uni.getStorageSync("access_token"),
					"fdbh": uni.getStorageSync("fdbh"),
					"sjbh": this.uFormTitle.detail_sj.split("-")[0],
					"spbm": spbm,
					"date": dayjs().format("YYYY-MM-DD"),
					"userid": uni.getStorageSync("userid"),
					"username": uni.getStorageSync("dlmc")
				}
				ZpdSjghjg(dataes).then((res) => {
					console.log("查找商品 直配进价、类型 信息 res", res)
					if (res.error_code == 0) {
						this.uFormModel.goods_zpjj = res.data_info.cgcxjg
						this.uFormModel.cxtype=res.data_info.cxtype
					} else {
						
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			// 扫码 搜索商品
			scan() {
				let reg = /^[0-9]{4,18}$/
				uni.scanCode({
					success: (res) => {
						console.log('扫码内容', res)
						if (reg.test(res.result)) {
							this.uFormModel.goods_spbm = res.result
						} else {
							this.$refs.uToast.show({
								type: "error",
								message: "条码识别失败。请重新扫码！"
							})
						}
					},
					fail: (err) => {
						this.$refs.uToast.show({
							type: "error",
							message: "识别失败"
						})
					}
				});
			},
			// 查询 特供（供价类型）
			formMore(lx,isauto) {
				this.searchCode=400
				let dataes={
					"access_token": uni.getStorageSync("access_token"),
					"dtype": "DMINFO",
					"companyid": uni.getStorageSync("companyid"),
				}
				Basic(dataes).then((res) => {
					console.log("!特供（供价类型）! 基本信息 res", res)
					if (res.error_code == 0) {
						let isyuyinBol=false
						if (isauto) {
							this.lxlist = res.data
						} else {
							if(this.isVoiceMode){
								isyuyinBol=true
							}
							let tempArr=[]
							for(var i in res.data){
								if(res.data[i].sjcxlxid.indexOf(lx)>-1){
									if(this.isVoiceMode){
										tempArr.push(res.data[i])
									}else{
										let xx = `${res.data[i].sjcxlxid}-${res.data[i].lxmc}`
										this.formMoreChange(xx,isyuyinBol)
									}
								}else if(res.data[i].lxmc.indexOf(lx)>-1){
									if(this.isVoiceMode){
										tempArr.push(res.data[i])
									}else{
										let xx = `${res.data[i].sjcxlxid}-${res.data[i].lxmc}`
										this.formMoreChange(xx,isyuyinBol)
									}
								}
							}
							this.selectData=tempArr
							this.searchCode=0
						}
					}else{
						this.$refs.uToast.show({
							type:"error",
							message: "查询失败"
						})
					}
				}).catch((err) => {
					console.log(err)
				})
			},
			formMoreChange(item,isauto){
				this.uFormModel.cxtype = item.split("-")[0]
				if(this.isVoiceMode){
					if(!isauto){// 语音模式时 手动点击进入
						this.clickYuyin("lx",true)
					}
				}
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
					"djtype": "ZPDBH",
					"fdbh": uni.getStorageSync("fdbh"),
					"userid": uni.getStorageSync("userid"),
				}
				OrderNew(dataes).then((res) => {
					if (res.error_code == 0) {
						this.uFormTitle.dh = res.djbh
						this.state = "add"
						this.queryMore("SJINFO", "detail_sj", true)
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
			titleChange(name, event) {
				this.uFormTitle[name] = event
				this.popupShow = false
			},
			
			ocrSave(arr) {
				// this.uploadarr = arr
				// for(var i in this.uploadarr){
				// 	this.$set(this.uploadarr[i], "guid", "")
				// 	this.$set(this.uploadarr[i], "scrq", dayjs().format("YYYY-MM-DD"))
				// }
				// // console.log("OCR 保存数据 this.uploadarr",this.uploadarr)
				// this.doSave("CHK",false)
			},
			// 保存事件函数。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。
			save() {
				this.$refs.uForm.validate().then(resf => {
					this.uploadarr = []
					this.uploadarr.push({
						"guid": "",
						"spbm": this.uFormModel.goods_spbm,
						"spsmm": this.uFormModel.goods_spsmm,
						"spmc": this.uFormModel.goods_spmc,
						"dbsl": this.uFormModel.goods_zpsl,
						"dbjg": this.uFormModel.goods_zpjj,
						"hsjj": this.uFormModel.hsjj,
						"cxjbz": this.uFormModel.cxtype,
						"nsjg": this.uFormModel.nsjg,
						"bzqts": this.uFormModel.bzqts,
						"spremark": this.uFormModel.goods_remark,
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
					"djbh": this.uFormTitle.dh,
					"sjbh": this.uFormTitle.detail_sj.split("-")[0],
					"fdbh": uni.getStorageSync("fdbh"),
					"shfdbh": this.uFormTitle.detail_fd.split("-")[0],
					"ydhrq": this.uFormTitle.detail_ddrq,
					"isimportjg": this.uFormTitle.isimportjg,
					"remark": this.uFormTitle.detail_remark,
					"list": this.uploadarr,
				}
				console.log("state=="+state+"; 保存商品 dosave dataes", dataes)
				ZpdDosave(dataes).then((res) => {
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
			},
			
			clearForm() {
				this.uFormModel.goods_spbm = ""
				this.uFormModel.goods_spsmm = ""
				this.uFormModel.goods_spmc = ""
				this.uFormModel.dw = ""
				this.uFormModel.gg = ""
				this.uFormModel.wlbzs = ""
				this.uFormModel.goods_zpsl = ""
				this.uFormModel.goods_zpjj = ""
				this.uFormModel.nsjg = ""
				this.uFormModel.jjje = ""
				this.uFormModel.hsjj = ""
				this.uFormModel.dqkcl = ""
				this.uFormModel.bzrq = ""
				this.uFormModel.goods_remark = ""
			},
			clearFocus() {
				this.focusObj.spbmFocus=false
				this.focusObj.numFocus=false
				this.focusObj.priceFocus=false
			},
			clearAlone(item) {
				if (this.state == "edit") {
					return
				}
				if (this.isVoiceMode) {
			
				} else {
					this.uFormModel[item] = ""
				}
			},
		},
		computed: {
			skin() {
				return this.$store.state.skin;
			}
		},
		watch: {
			"state": function(newv, oldv) {
				if (newv == "add") {
					this.neworderShow = true
					setTimeout(() => {
						this.neworderShow = false
					}, 4000)
				} else {
					this.neworderShow = false
				}
			},
			"uFormModel.goods_spbm": function(newv, oldv) {
				if (newv.length == 0) {
					this.isSpComplete = false
					this.clearForm()
					this.clearFocus()
				} else {
		
				}
			},
			"uFormModel.goods_zpsl": function(newv, oldv) {
				let xx = Number(this.uFormModel.goods_zpjj).toFixed(2) * 10000
				this.uFormModel.jjje = newv * 10000 * xx / 10000/10000
			},
			"uFormModel.goods_zpjj": function(newv, oldv) {
				let xx = Number(this.uFormModel.goods_zpsl) * 10000
				this.uFormModel.jjje = Number(newv).toFixed(2) * 10000 * xx / 10000/10000
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
					padding: 10px 10px 0;
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

			.form-card {
				padding: 10px 15px;
				background-color: #fff;
				border-bottom-left-radius: 10px;
				border-bottom-right-radius: 10px;
				margin-bottom: 10px;
				box-sizing: border-box;

				.shopTishi {
					width: 100%;
					display: flex;
					justify-content: space-between;
					align-items: center;

					.shopTishi-view {
						uni-text {
							font-size: 14px;
							color: #999;
							font-weight: 400;
						}
					}

					.shopTishi-view-half {
						width: 50%;

						uni-text {
							font-size: 14px;
							color: #999;
							font-weight: 400;
						}
					}

					.left-con {
						margin-right: 4px;
					}

					.show-dots {
						overflow: hidden;
						text-overflow: ellipsis;
						display: -webkit-box;
						-webkit-line-clamp: 2;
						-webkit-box-orient: vertical;
					}
				}

				.inp-right-text {
					display: inline-block;
					width: 19px;
				}

				.radio-view {
					display: flex;
					justify-content: space-between;
					align-items: center;

					.radio-text {
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

				.u-form-item__body__right__content__slot {
					display: flex;
					justify-content: space-between;
				}

				.shoping {
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