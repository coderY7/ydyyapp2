<template>
  <view class="box-compents">
    <!-- 编辑单据 内容 -->
    <view v-show="stateDetail">
      <view class="edit-title">单号:{{title.djbh}}</view>
      <u-form class="form-card" labelPosition="left" :model="editForm" :rules="editRules" ref="uForm">
        <u-form-item label="商品编码" :labelWidth="74" prop="spbm" class="shoping">
          <u-input placeholder="请输入商品编码/名称/简码" disabled v-model="editForm.spbm">
          </u-input>
        </u-form-item>
        <view>
          <view class="shopTishi">
            <view class="shopTishi-view show-dots" v-if="editForm.spmc">
              <text class="left-con">名称-条码:</text>
              <text>{{editForm.spmc}}</text>-
              <text>{{editForm.spsmm}}</text>
            </view>
          </view>
          <view class="shopTishi">
            <view class="shopTishi-view-half" v-if="editForm.dw">
              <text class="left-con">单位:</text>
              <text>{{editForm.dw}}</text>
            </view>
            <view class="shopTishi-view-half" v-if="editForm.gg">
              <text class="left-con">规格:</text>
              <text>{{editForm.gg}}</text>
            </view>
          </view>
          <view class="shopTishi">
            <view class="shopTishi-view-half" v-if="editForm.nsjg">
              <text class="left-con">零售价格:</text>
              <text>{{editForm.nsjg}}</text>
            </view>
          </view>
        </view>





        <view  v-if="uFormTitle.cxlxid=='01'">
          <u-form-item  label="数量下限" :labelWidth="74" prop="slxx" style="margin-bottom: 10px">
            <u-input placeholder="请输入数量下限" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

          <u-form-item  label="数量上限" :labelWidth="74" prop="slsx" style="margin-bottom: 10px">
            <u-input placeholder="请输入数量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

          <u-form-item  label="赠送商品编码" :labelWidth="74" prop="zsspbm" style="margin-bottom: 10px">
            <u-input placeholder="请输入赠送商品编码" type="text"  v-model="editForm.zsspbm"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

          <u-form-item  label="赠送数量" :labelWidth="74" prop="zssl" >
            <u-input placeholder="请输入赠送数量" type="text"  v-model="editForm.zssl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='02'||uFormTitle.cxlxid=='18'||uFormTitle.cxlxid=='28'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" >
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='03'||uFormTitle.cxlxid=='19'||uFormTitle.cxlxid=='29'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>


        <view v-if="uFormTitle.cxlxid=='04'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="按量下限" :labelWidth="74" prop="slxx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入按量下限" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="按量上限" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入按量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>


        <view v-if="uFormTitle.cxlxid=='05'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="按量下限" :labelWidth="74" prop="slxx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入按量下限" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="按量上限" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入按量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='B1'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最底销量" :labelWidth="74" prop="slxx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入最底销量" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="销量上限" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入销量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='B2'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最底销量" :labelWidth="74" prop="slxx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入最底销量" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="销量上限" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入销量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='06'||uFormTitle.cxlxid=='21'||uFormTitle.cxlxid=='31'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>


        </view>

        <view v-if="uFormTitle.cxlxid=='07'||uFormTitle.cxlxid=='20'||uFormTitle.cxlxid=='30'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1">
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>


        </view>

        <view v-if="uFormTitle.cxlxid=='08'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="限销数量" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入限销数量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

        </view>

        <view v-if="uFormTitle.cxlxid=='09'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="限销数量" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入限销数量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

        </view>

        <view v-if="uFormTitle.cxlxid=='10'||uFormTitle.cxlxid=='11'">
          <u-form-item label="递增特价1" :labelWidth="74" prop="cxzk1" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价1" type="text"  v-model="editForm.cxzk1"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价2" :labelWidth="74" prop="cxzk2" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价2" type="text"  v-model="editForm.cxzk2"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价3" :labelWidth="74" prop="cxzk3" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价3" type="text"  v-model="editForm.cxzk3"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价4" :labelWidth="74" prop="cxzk4" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价4" type="text"  v-model="editForm.cxzk4"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价5" :labelWidth="74" prop="cxzk5" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价5" type="text"  v-model="editForm.cxzk5"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价6" :labelWidth="74" prop="cxzk6" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价6" type="text"  v-model="editForm.cxzk6"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="递增特价7" :labelWidth="74" prop="cxzk7" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入递增特价7" type="text"  v-model="editForm.cxzk7"
            >
            </u-input>
          </u-form-item>
          <u-form-item label="第N件数" :labelWidth="74" prop="xspsl" v-show="doingindex>=1">
            <u-input placeholder="请输入第N件数" type="text"  v-model="editForm.xspsl"
            >
            </u-input>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='A0'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="换购价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入换购价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最高换购数量" :labelWidth="74" prop="slsx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入最高换购数量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="换购数量" :labelWidth="74" prop="zssl" v-show="doingindex>=1">
            <u-input placeholder="请输入换购数量" type="text"  v-model="editForm.zssl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='C1'">
          <u-form-item label="执行扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入执行扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

        </view>

        <view v-if="uFormTitle.cxlxid=='A1'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="执行折扣" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入执行折扣" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='A2'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="优惠金额" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入优惠金额" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="是否开启金额倍数累加选项" :labelWidth="74" prop="issum" v-show="doingindex>=1">
            <view>
              <switch  color="#FFCC33" style="transform:scale(0.7)" @change="isissum"/>
            </view>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='A3'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="抵挡金额" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入抵挡金额" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="上限金额" :labelWidth="74" prop="sxje" v-show="doingindex>=1">
            <u-input placeholder="请输入上限金额" type="text"  v-model="editForm.sxje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='A4'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="购物券面值" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入购物券面值" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="购物券ID" :labelWidth="74" prop="gwjid" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入购物券ID" type="text"  v-model="editForm.gwjid"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="返券数量" :labelWidth="74" prop="zssl" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入返券数量" type="text"  v-model="editForm.zssl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="是否开启金额倍数累加选项" :labelWidth="74" prop="issum" v-show="doingindex>=1">
            <view>
              <switch  color="#FFCC33" style="transform:scale(0.7)" @change="isissum"/>
            </view>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='A5'">
          <u-form-item label="买满金额" :labelWidth="74" prop="mmje" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入买满金额" type="text"  v-model="editForm.mmje"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="数量上限" :labelWidth="74" prop="slsx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入数量上限" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="优惠数量" :labelWidth="74" prop="zssl" v-show="doingindex>=1">
            <u-input placeholder="请输入优惠数量" type="text"  v-model="editForm.zssl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='D0'">
          <u-form-item label="换购数量" :labelWidth="74" prop="zssl" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入换购数量" type="text"  v-model="editForm.zssl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="换购价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入换购价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最高换购量" :labelWidth="74" prop="slsx" v-show="doingindex>=1">
            <u-input placeholder="请输入最高换购量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='22'">
          <u-form-item label="促销价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入促销价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="限购次数/量" :labelWidth="74" prop="slsx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入限购次数/量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="是否开启按次控制" :labelWidth="74" prop="issum" v-show="doingindex>=1">
            <view>
              <switch  color="#FFCC33" style="transform:scale(0.7)" @change="isissum"/>
            </view>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='23'">
          <u-form-item label="领赠价格" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入领赠价格" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="限领数量" :labelWidth="74" prop="slsx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入限领数量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="是否开启限生日月" :labelWidth="74" prop="isbirth" v-show="doingindex>=1">
            <view>
              <switch  color="#FFCC33" style="transform:scale(0.7)" @change="isisbirth"/>
            </view>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='24'">
          <u-form-item label="生日特价" :labelWidth="74" prop="cxjg" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入生日特价" type="text"  v-model="editForm.cxjg"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最高限量" :labelWidth="74" prop="slsx" v-show="doingindex>=1" style="margin-bottom: 10px">
            <u-input placeholder="请输入最高限量" type="text"  v-model="editForm.slsx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
          <u-form-item label="最低限量" :labelWidth="74" prop="slxx" v-show="doingindex>=1">
            <u-input placeholder="请输入最低限量" type="text"  v-model="editForm.slxx"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>
        </view>

        <view v-if="uFormTitle.cxlxid=='25'">
          <u-form-item label="促销折扣率" :labelWidth="74" prop="cxzkl" v-show="doingindex>=1">
            <u-input placeholder="请输入促销折扣率" type="text"  v-model="editForm.cxzkl"
            >
            </u-input>
            <uni-icons custom-prefix="iconfont" type="icon-yuyin"
                       :color="doingId=='num'?'#358CC9':'#7A7A7A'" size="19" v-if="isVoiceMode"></uni-icons>
            <text class="inp-right-text" v-else></text>
          </u-form-item>

        </view>









        <u-form-item label="特供扣点" :labelWidth="74" prop="checkdm" >
          <view>
            <switch  color="#FFCC33" style="transform:scale(0.7)" @change="ischeckdm"/>
          </view>
        </u-form-item>

        <u-form-item v-if="editForm.checkdm" label="折扣类型" :labelWidth="74" prop="dmkdlxid" >
          <uni-data-select
              v-model="editForm.dmkdlxid"
              :localdata="dmkdlxidlist"
              :clear="false"
          ></uni-data-select>
        </u-form-item>

        <u-form-item v-if="editForm.checkdm" label="商家合同" :labelWidth="74" prop="sjbh">
          <uni-data-select
              v-model="editForm.sjinfo[0].sjbh"
              :localdata="sjlist"
              :clear="false"
          ></uni-data-select>

        </u-form-item>

        <u-form-item v-if="editForm.checkdm" label="特供进价" :labelWidth="74" prop="dmpjjj">
          <u-input placeholder="请输入特供进价" type="number" v-model="editForm.dmpjjj">
          </u-input>
        </u-form-item>
      </u-form>

      <view class="btns" v-if="stateDetail">
        <u-button type="primary" class="my-primary-button" :plain="true" text="取消" throttleTime="2000"
                  @tap="cancelDetail"></u-button>
        <u-button type="primary" class="my-primary-button" text="保存" throttleTime="2000" @tap="editDetailSave">
        </u-button>
      </view>
    </view>
    <!--  -->
    <view class="foldGroup" v-show="!stateDetail">
      <view class="fold-title" v-if="title.state!='add'">
        <view class="fold-title-t fold-title-flex-start">
          <text>单号: {{title.djbh}}</text>
        </view>
      </view>
      <view class="fold-content" v-for="(item,index) in tableData">
        <view class="card-flex-wrap">
          <view class="card-row">{{item.spmc}}</view>
          <view>
            <uni-icons type="compose" size="20" color="#3183C1" v-if="title.state=='add'||title.state=='edit'||title.state=='pladd'" @tap="toeditDetail(item,index)"></uni-icons>
            <uni-icons type="trash" size="20" color="#F13B44" style="margin-left:10px;" v-if="title.state=='add'||title.state=='edit'||title.state=='pladd'" @tap="delGoods(item,index)"></uni-icons>
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
            <text class="left-con">零售价格:</text>
            <text class="right-con">{{item.nsjg}}</text>
          </view>
          <view class="multiple-con view-flex">
            <text class="left-con">促销价格:</text>
            <text class="right-con">{{item.cxjg}}</text>
          </view>
        </view>
        <view class="multiples">
          <view class="multiple-con view-flex">
            <text class="left-con">特供进价:</text>
            <text class="right-con" style="font-size: 8px">{{item.cxspjj}}</text>
          </view>
          <view class="multiple-con view-flex">
            <text class="left-con">商家编号:</text>
            <text class="right-con" style="font-size: 8px">{{item.sjbh}}</text>
          </view>
        </view>
      </view>
    </view>


    <!-- 弹窗。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。 -->
    <u-toast ref="uToast"></u-toast>


  </view>
</template>

<script>
import dayjs from "dayjs";
import {
  Basic,
  Search,
  CxdDelLine,
  Cxdsjinfo,
  CxdUpdate
} from "@/network/api.js";
import xuanSwitch from "@/components/xuan-switch/xuan-switch.vue";
export default {
  props: {
    tableData: {
      type: Array,
      default: () => {
        return []
      }
    },
    title: {
      type: Object,
      default: () => {
        return {}
      }
    },
    uFormTitle: {
      type: Object,
      default: () => {
        return {}
      }
    },
    state:{
      type: String,
      default: () => {
        return ""
      }
    },
  },
  components: {
    xuanSwitch
  },
  data() {
    return {
      isVoiceMode:false,
      doingindex:100,
      start:'2021-3-20',
      switchList:["是","否"],
      dmkdlxidlist:[],
      sjlist:[],
      editForm:{
        dw: "",
        gg: "",
        bysl: "",
        splx: false,//赠送商品
        jgcxbz: "",//供价类型

        allsmm: true,
        bcbl: '',
        checkcbj: false,
        checkdm: false,
        cxjg: '',
        cxzk1: '',
        cxzk2: '',
        cxzk3: '',
        cxzk4: '',
        cxzk5: '',
        cxzk6: '',
        cxzk7: '',
        cxzkl: '',
        dmkdlxid: "",
        dmnewkdl: '',
        dmpjjj: "",
        dmsjbh: "",
        gwjid: "",
        isbirth: false,
        issum: false,
        mmje: 0,
        nsjg: '',
        saveStatus: true,
        sjinfo: [
          {
            sjbh: "",
            sjmc: ""
          }
        ],
        slsx: 0,
        slxx: 0,
        spbm: "",
        spmc: "",
        spsmm: "",
        sxje: 0,
        type: "cxUpdate",
        xsps: [
          "X"
        ],
        zssl: 0,
        zsspbm: "X"


      },
      editRules:{
        "nsjg": [{
          type: "number",
          required: true,
          message: "请填写零售价格",
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
        ],
        "cxjg": [{
          type: "number",
          required: true,
          message: "请填写促销价格",
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
        ],
        "cxspjj": [{
          type: "number",
          required: true,
          message: "请填写特供价格",
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
        ],
      },
      serchGoodsData:[],
      lxlist:[],
      stateDetail: false,
      tableIndex: -1,
      jglxdata:[],
      uFormTitle:{}
    }
  },
  created(){
    console.log('created',this.editForm)
  },
  mounted() {
    console.log("edit tableData",this.tableData)
    console.log("edit this.title",this.title)
    this.formMore("",true)
    //价格类型
    this.jglxdata=uni.getStorageSync('basic').SPJGMS
    let jglist = []
    this.jglxdata.forEach((item,i)=>{
      jglist.push({
        "value": this.jglxdata[i].jglxid,
        "text": this.jglxdata[i].jglxmc,
        "is_selected":false
      })
    })
    this.jglxdata=jglist
  },
  methods: {
    //折扣类型
    zklx(){
      let dmkdlxidlist=uni.getStorageSync('basic').KDFSINFO
      dmkdlxidlist.splice(0,0,{yjkdlxid:'NOT',yjkdlxm:'不设置新折扣'})
      dmkdlxidlist.forEach((item,i)=>{
        this.dmkdlxidlist.push({
          "value": dmkdlxidlist[i].yjkdlxid,
          "text": `${dmkdlxidlist[i].yjkdlxid}-${dmkdlxidlist[i].yjkdlxm}`,
        })
      })
      this.editForm.dmkdlxid=this.dmkdlxidlist[0].value
    },
    //是否开启金额倍数累加选项
    isissum(e){
      this.uFormModel.issum=e.detail.value
    },
    //是否开启限生日月
    isisbirth(e){
      this.uFormModel.isbirth=e.detail.value
    },
    //库存补差
    ischeckcbj(e){
      this.uFormModel.checkcbj=e.detail.value
    },
    //特供扣点
    ischeckdm(e){
      this.editForm.checkdm=e.detail.value
    },
    changeLog(e) {
      console.log("-change事件:", e,this);
    },
    jglxChanged(e) {
      let jglxlist=[]
      e.forEach((item,i)=>{
        if(item.is_selected){
          jglxlist.push(item.value)
        }
      })

      this.editForm.jglxid=jglxlist
      this.jglxdata = e
      console.log(this.editForm.jglxid)
      if(this.editForm.jglxid.length=='1'){
        this.editForm.jglxid=this.editForm.jglxid.toString()
      }
    },
    //商家合同
    SjhtChange(){
      let data={
        access_token:uni.getStorageSync('access_token'),
        fdbh:uni.getStorageSync('fdbh'),
        spbm:this.editForm.spbm,
        userid:uni.getStorageSync('userid'),
      }
      Cxdsjinfo(data).then((res)=>{
        if(res.error_code==0){
          //默认值
          this.editForm.sjinfo[0].sjbh=res.data[0].sjbh
          this.sjlist=res.data
          let sjlist = []
          this.sjlist.forEach((item,i)=>{
            sjlist.push({
              "value":  this.sjlist[i].sjbh,
              "text":  this.sjlist[i].sjmc,
            })
          })
          this.sjlist=sjlist
        }
      })
      console.log(this.editForm)
    },
    // 查询 特供（供价类型）
    formMore(lx,isAll) {
      let dataes={
        "access_token": uni.getStorageSync("access_token"),
        "dtype": "DMINFO",
        "companyid": uni.getStorageSync("companyid"),
      }
      Basic(dataes).then((res) => {
        if (res.error_code == 0) {
          if(isAll){
            this.lxlist=res.data
          }else{
            for(var i in res.data){
              if(res.data[i].sjcxlxid.indexOf(lx)>-1){
                let xx = res.data[i].sjcxlxid
                this.formMoreChange(xx)
              }
            }
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
    formMoreChange(item){
      this.editForm.jgcxbz = item
    },
   //获取当前对应数据
    currentdata(){
      console.log(this.uFormTitle)
      if(this.uFormTitle.cxlxid=='01'){
        this.editForm.slxx=''
        this.editForm.slsx=''
        this.editForm.zsspbm=''
        this.editForm.zssl=''
      }
      if(this.uFormTitle.cxlxid=='02'||this.uFormTitle.cxlxid=='18'||this.uFormTitle.cxlxid=='28'||this.uFormTitle.cxlxid=='07'||this.uFormTitle.cxlxid=='20'||this.uFormTitle.cxlxid=='30'){
        this.editForm.cxjg=''
      }
      if(this.uFormTitle.cxlxid=='03'||this.uFormTitle.cxlxid=='19'||this.uFormTitle.cxlxid=='29'||this.uFormTitle.cxlxid=='06'||this.uFormTitle.cxlxid=='21'||this.uFormTitle.cxlxid=='31'){
        this.editForm.cxzkl=''
      }
      if(this.uFormTitle.cxlxid=='04'){
        this.editForm.slxx=''
        this.editForm.slsx=''
        this.editForm.cxjg=''
      }
      if(this.uFormTitle.cxlxid=='05'){
        this.editForm.slxx=''
        this.editForm.slsx=''
        this.editForm.cxzkl=''
      }
      if(this.uFormTitle.cxlxid=='B1'){
        this.editForm.slxx=''
        this.editForm.slsx=''
        this.editForm.cxjg=''
      }
      if(this.uFormTitle.cxlxid=='B2'){
        this.editForm.slxx=''
        this.editForm.slsx=''
        this.editForm.cxzkl=''
      }

      if(this.uFormTitle.cxlxid=='08'){
        this.editForm.cxjg=''
        this.editForm.slsx=''
      }
      if(this.uFormTitle.cxlxid=='09'){
        this.editForm.slsx=''
        this.editForm.cxzkl=''
      }
      if(this.uFormTitle.cxlxid=='10'||this.uFormTitle.cxlxid=='11'){
        this.editForm.cxzk1=''
        this.editForm.cxzk2=''
        this.editForm.cxzk3=''
        this.editForm.cxzk4=''
        this.editForm.cxzk5=''
        this.editForm.cxzk6=''
        this.editForm.cxzk7=''
        this.editForm.xspsl=''
      }
      if(this.uFormTitle.cxlxid=='A0'){
        this.editForm.mmje=''
        this.editForm.cxjg=''
        this.editForm.slsx=''
        this.editForm.zssl=''
      }
      if(this.uFormTitle.cxlxid=='C1'){
        this.editForm.cxzkl=''
      }
      if(this.uFormTitle.cxlxid=='A1'){
        this.editForm.mmje=''
        this.editForm.cxzkl=''
      }
      if(this.uFormTitle.cxlxid=='A2'){
        this.editForm.mmje=''
        this.editForm.cxjg=''
        this.editForm.issum=false

      }
      if(this.uFormTitle.cxlxid=='A3'){
        this.editForm.mmje=''
        this.editForm.cxjg=''
        this.editForm.sxje=''

      }
      if(this.uFormTitle.cxlxid=='A4'){
        this.editForm.mmje=''
        this.editForm.cxjg=''
        this.editForm.gwjid=''
        this.editForm.issum=false
        this.editForm.zssl=''

      }
      if(this.uFormTitle.cxlxid=='A5'){
        this.editForm.mmje=''
        this.editForm.slsx=''
        this.editForm.zssl=''
      }
      if(this.uFormTitle.cxlxid=='D0'){
        this.editForm.zssl=''
        this.editForm.cxjg=''
        this.editForm.slsx=''
      }
      if(this.uFormTitle.cxlxid=='22'){
        this.editForm.issun=false
        this.editForm.cxjg=''
        this.editForm.slsx=''
      }
      if(this.uFormTitle.cxlxid=='23'){
        this.editForm.isbirth=false
        this.editForm.cxjg=''
        this.editForm.slsx=''
      }
      if(this.uFormTitle.cxlxid=='24'){
        this.editForm.slxx=''
        this.editForm.cxjg=''
        this.editForm.slsx=''
      }
      if(this.uFormTitle.cxlxid=='25'){
        this.editForm.cxzkl=''
      }
    },
    // 编辑商品
    toeditDetail(row, index) {
      this.uFormTitle=this.uFormTitle
      console.log(row,this.uFormTitle);

      this.currentdata()
      //this.serchGoods(row.spbm,this.editForm)
      // this.editForm.jgcxbz = row.jgcxbz
      this.editForm.splx = row.splx=="T"?true:false
      this.editForm.sjbh = row.fdsjbh
      this.editForm.nsjg = row.nsjg
      this.editForm.remark=row.remark
      this.editForm.spmc=row.spmc
      this.editForm.spbm=row.spbm
      this.editForm.userid=row.userid
      this.editForm.fdssbl=row.fdssbl
      this.editForm.fdsjbh=row.fdsjbh
      this.editForm.sxsj=row.sxsj

      this.editForm.cxjg=row.cxjg

      this.editForm.cxzkl=row.cxzkl //折扣率
      this.editForm.fdbm=row.fdbm
      this.editForm.gwjid=row.gwjid
      this.editForm.kcbjbz=row.kcbjbz
      this.editForm.pfjg=row.pfjg
      this.editForm.yjkdlxid=row.yjkdlxid
      this.editForm.cxlxid=row.cxlxid
      this.editForm.cxsl=row.cxsl //最小数量
      this.editForm.cxsl2=row.cxsl2 //最大数量
      this.editForm.yxsl=row.yxsl
      this.editForm.zssl=row.zssl
      this.editForm.zsspbm=row.zsspbm



      this.formMore(row.jgcxbz,false)
      // this.$set(this.tableData[index], "splx", [this.tableData[index].splx])
      this.stateDetail = true
      this.tableIndex = index
      this.SjhtChange()
      console.log("编辑商品 row",row,this.uFormTitle)
      this.zklx()




    },
    cancelDetail() {
      this.editForm.spbm= ""
      this.editForm.spsmm= ""
      this.editForm.spmc= ""
      this.editForm.dw= ""
      this.editForm.gg= ""
      this.editForm.jgcxbz= ""//供价类型
      this.editForm.splx= false//赠送商品
      this.editForm.bysl= ""
      this.editForm.nsjg= ""
      this.stateDetail = false
      this.tableIndex = -1
    },

    switChange(val){
      this.editForm.splx=val
    },
    // 保存商品
    editDetailSave() {
      //this.$refs.uForm.validate().then(resf => {
      this.uploadarr = []
      // let xx = Number(this.tableData[this.tableIndex].rq.split("T")[0].split("-")[2]) + this.serchGoodsData.bzqts
      console.log('2121',this.editForm)
      this.uploadarr.push({

        "guid": this.tableData[this.tableIndex].recordid,
        "sppc":'',
        "bhjg":this.editForm.bhjg,
        "bqjg":this.tableData[this.tableIndex].bqjg,
        "fdssbl":this.editForm.fdssbl,//分摊比率
        "lsh":this.editForm.bjlsh,
        "sjbh":this.editForm.sjbh,//分摊商家
        "spfixlx":this.editForm.spfixlx,
        "spremark":this.editForm.spremark,
        "sxsj":this.tableData[this.tableIndex].sxsj,//生效时间

        mmje:this.editForm.mmje,
        slsx:this.editForm.slsx,
        slxx:this.editForm.slxx,
        zsspbm:this.editForm.zsspbm,
        zssl:this.editForm.zssl,
        gwjid:this.editForm.gwjid,
        sxje:this.editForm.sxje,
        issum:this.editForm.issum,
        cxzkl:this.editForm.cxzkl,
        xspsl:this.editForm.xspsl,

        cxzk1:this.editForm.cxzk1,
        cxzk2:this.editForm.cxzk2,
        cxzk3:this.editForm.cxzk3,
        cxzk4:this.editForm.cxzk4,
        cxzk5:this.editForm.cxzk5,
        cxzk6:this.editForm.cxzk6,
        cxzk7:this.editForm.cxzk7,



        allsmm: this.editForm.allsmm,//是否所有商品
        bcbl:this.editForm.bcbl,//补差比例
        checkcbj: this.editForm.checkcbj,//是否库存补差
        checkdm: this.editForm.checkdm,//是否特供
        cxjg:this.editForm.cxjg,//促销价格
        dmkdlxid:this.editForm.dmkdlxid,//促扣类型
        dmnewkdl:this.editForm.dmnewkdl,//新促扣率
        dmpjjj: this.editForm.dmpjjj,//特供进价
        dmsjbh:this.editForm.sjbh,//特供商家编号
        nsjg: this.editForm.nsjg,//零售价格
        saveStatus: true,
        sjinfo: [{
          sjbh: this.editForm.sjinfo[0].sjbh,
          sjmc: this.editForm.sjinfo[0].sjmc
        }],
        type: this.editForm.type,
        "spbm": this.editForm.spbm,
        "spmc": this.editForm.spmc,
        "spsmm": this.editForm.spsmm,
      })

      // console.log("保存商品 editDetailSave this.uploadarr",this.uploadarr)
      //this.$emit("editSave",this.uploadarr,this.editForm)
      // }).catch(errors => {
      //
      // })
      this.saveface(this.uploadarr)
    },
    //保存接口
    saveface(list){
      let data={
        "access_token": uni.getStorageSync("access_token"),
        "djbh": this.uFormTitle.djbh,
        "fdbh": uni.getStorageSync("fdbh"),
        "userid": uni.getStorageSync("userid"),
        EndRQ:this.uFormTitle.EndRQ,
        StartRQ:this.uFormTitle.StartRQ,
        cxlxid: this.uFormTitle.cxlxid,
        fdlist:this.uFormTitle.fdlist,
        weeklist:'',
        remark:'',
        "list": this.uploadarr,
      }
      CxdUpdate(data).then((res)=>{
        console.log('保存商品修改',res);
      })
    },

    // 删除商品
    delGoods(row, index) {
      console.log(row)
      uni.showModal({
        content: "是否确认删除商品",
        success: (resm)=> {
          if (resm.confirm) {
            let dataes={
              "access_token": uni.getStorageSync("access_token"),
              "djbh": row.cxxxid,
              "fdbh": uni.getStorageSync("fdbh"),
              "userid": uni.getStorageSync("userid"),
              "username": uni.getStorageSync("dlmc"),
              "list": [{
                "guid": row.recordid,
                "spbm": row.spbm,
                "spmc": row.spmc,
                "spsmm": row.spsmm
              }]
            }
            console.log("删除商品 dataes",dataes)
            CxdDelLine(dataes).then((res) => {
              if (res.error_code == 0) {
                // this.$parent.getList()
                this.$emit("delgoods")
                this.$refs.uToast.show({
                  type: "success",
                  message: "删除成功"
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
          } else if (resm.cancel) {

          }
        }
      });
    },
    serchGoods(val) {
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
        if (res.error_code == 0) {
          this.serchGoodsData = res.data[0]
          this.editForm.spbm=res.data[0].spbm
          this.editForm.spsmm=res.data[0].spsmm
          this.editForm.spmc=res.data[0].spmc
          this.editForm.dw=res.data[0].dw
          this.editForm.gg=res.data[0].gg
          this.editForm.spremark=res.data[0].spremark
          this.editForm.spfixlx=res.data[0].spfixlx

        }
      }).catch((err) => {
        console.log(err)
      })
    },

  },
  watch: {

  }

}
</script>

<style lang="scss" scoped>
.box-compents {
  .edit-title {
    padding:10px;
    color: #fff;
    border-top-left-radius: 10px;
    border-top-right-radius: 10px;
    background-color: #358CC9;
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
  .btns {
    display: flex;
    justify-content: space-around;
    margin-top: 8px;

    /deep/.u-button {
      width: 33%;
      margin: 0;
    }
  }

  .foldGroup {
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
    }

    .fold-content>* {
      margin-bottom: 10px;
    }

    .fold-content>*:last-child {
      margin-bottom: 0px;
    }
  }

  .lxactive{
    background-color: #358CC9 !important;
    color: #fff;
  }


  .flex-btns {
    display: flex;
    justify-content: space-between;

    /deep/.u-button--primary.my-primary-button {
      width: 28%;
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
