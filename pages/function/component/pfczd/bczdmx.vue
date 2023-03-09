<template>
<view>

  <view class="collshop-box">
    <!--            <view class="collshop-top" @click="isedit">{{edit?'完成':'编辑'}}</view>-->
    <view class="collshop-cen">
      <checkbox-group class="block" @change="changeCheckbox">
        <block v-for="item in curvedata" :key="item['商品条码']">
          <view class="collshop-cen-item">
            <checkbox  style="margin: auto 0"  :value="JSON.stringify(item)"
                       :checked="checkedArr.includes(JSON.stringify(item))"
                       :class="{'checked':checkedArr.includes(JSON.stringify(item))}"></checkbox>

            <view class="right unit3" @click="itemdata(item)">

              <view class="unit3box" >
                <view class="title"> <span style="color: #4f99ff;font-size: 18px">{{item['商品名称']}}</span></view>
                <view class="title">可冲数量:
                  <span style="color: red;">{{item['可冲数量']}}</span>
                </view>
              </view>
              <view class="unit3box">
                <view class="title">商品条码:
                  <span style="color: red;">{{item['商品条码']}}</span>
                </view>
                <view class="title">商品编码:
                  <span style="color: red;">{{item['商品编码']}}</span>
                </view>
              </view>

              <view class="unit3box">
                <view class="title">冲帐数量:{{item['冲帐数量']}}</view>
                <view class="title">冲后价格:{{item['冲后价格']}}</view>
              </view>

              <view class="unit3box">
                <view class="title">销售数量:{{item['销售数量']}}</view>
                <view class="title">销售价格:{{item['销售价格']}}</view>
              </view>

            </view>
          </view>
        </block>
      </checkbox-group>
    </view>
    <view  class="collshop-bot">
      <view>
        <checkbox-group @change="allChoose" >
          <label>
            <checkbox value="all" :class="{'checked':allChecked}" :checked="allChecked?true:false">
            </checkbox> 全选
          </label>
        </checkbox-group>
      </view>



<!--      <view class="right" @click="verify" >合帐</view>-->
      <view class="right" @click="verify" >确认</view>
    </view>
  </view>

</view>
</template>

<script>
export default {
  name: "bczdmx",
  props:{
    curvedata: {
      type: Array,
      default: ''
    }
  },

  data(){
    return{
      xzdata:[],//选中的数据
      radiovalue:'PA',
      hzlist:'',
      gztype:'',
      result:'',
      bdt:'',
      sumdata:'',

      checkedArr: [], //复选框选中的值
      allChecked: false //是否全选
    }
  },
  mounted() {
  	console.log('111',this.curvedata,)
  },
  methods:{
    // 多选复选框改变事件
    changeCheckbox(e) {
      this.checkedArr = e.detail.value;
      let hzlist=[]
      e.detail.value.forEach((item)=>{
        hzlist.push(JSON.parse(item))
      })
      this.hzlist=hzlist

      let curvedata=[]
      e.detail.value.forEach((item)=>{
        curvedata.push(JSON.parse(item))
      })
     // console.log(this.hzlist)
      // 如果选择的数组中有值，并且长度等于列表的长度，就是全选
      if (this.checkedArr.length > 0 && this.checkedArr.length == this.curvedata.length) {
        this.allChecked = true;
      } else {
        this.allChecked = false;
      }
    },
    // 全选事件
    allChoose(e) {
      let chooseItem = e.detail.value;
      // 全选
      if (chooseItem[0] == 'all') {
        this.allChecked = true;
        for (let item of this.curvedata) {
          let itemVal =JSON.stringify(item)
          if (!this.checkedArr.includes(itemVal)) {
            this.checkedArr.push(itemVal);

            let hzlist=[]
            this.checkedArr.forEach((item)=>{
              hzlist.push(JSON.parse(item))
            })
            this.hzlist=hzlist

            let curvedata=[]
            this.checkedArr.forEach((item)=>{
              curvedata.push(JSON.parse(item))
            })
            // console.log(this.hzlist)

          }
        }
      } else {
        // 取消全选
       // this.$emit("send", [])
        this.allChecked = false;
        this.checkedArr = [];
      }
    },
    //确认
    verify(){
      console.log('选择的商品',this.hzlist)
    }
  }
}
</script>

<style scoped>

</style>
<style lang="scss">
checkbox .wx-checkbox-input {
  border-radius: 50% !important;
  color: #ffffff !important;
}
checkbox .wx-checkbox-input.wx-checkbox-input-checked {
  color: #fff;
  background: #550000;
  border: 4upx solid #010155;
}
.wx-checkbox-input.wx-checkbox-input-checked {
  border: none !important;
}
page {
  background-color: #eee;
}
.hz_type{
  width: 100%;
  padding: 20rpx;
}
.collshop-box {
  width: 100%;
  height: auto;
  box-sizing: border-box;
  overflow: hidden;
}

.collshop-top {
  width: 100%;
  height: 88rpx;
  line-height: 88rpx;
  text-align: right;
  font-size: 24rpx;
  font-family: Microsoft YaHei;
  font-weight: 400;
  color: #E10800;
  padding: 0 22rpx;
  box-sizing: border-box;
}

.collshop-cen {
  width: 100%;
  height: auto;
  overflow: hidden;
}

.collshop-cen-item {
  display: flex;
  align-items: center;
  width: 100%;
  padding:0 20rpx;
  box-sizing: border-box;
  background-color: #fff;
  margin-bottom: 20rpx;
  overflow: hidden;
}

.collshop-cen-item .left {
  float: left;
  width: 92rpx;
  height: 92rpx;
  border-radius: 12rpx;
}

.collshop-cen-item .right {
  float: left;
  margin-left: 24rpx;
}

.collshop-cen-item .right .title {
  font-size: 28rpx;
  font-family: Microsoft YaHei;
  font-weight: 400;
  color: #333;
}

.collshop-cen-item .right .dengjiimg {
  width: 76rpx;
  height: 30rpx;
  margin-top: 12rpx;
}

.collshop-cen-item checkbox {
  float: left;
  width: 40rpx;
  height: 40rpx;
  margin: 16rpx 36rpx 0 0;
}

.collshop-bot {
  position: fixed;
  left: 0;
  bottom: 0;
  width: 100%;
  height: 100rpx;
  line-height: 92rpx;
  overflow: hidden;
  padding: 0 22rpx;
  box-sizing: border-box;
  background-color: #fff;
  display: flex;
  justify-content: space-between;
}

.collshop-bot checkbox-group {
  float: left;
}

.collshop-bot .right {
  float: right;
  width: 170rpx;
  height: 70rpx;
  line-height: 70rpx;
  text-align: center;
  background: #E10800;
  font-size: 30rpx;
  font-family: Microsoft YaHei;
  font-weight: 400;
  color: #FFF;
  border-radius: 6rpx;
  margin-top: 15rpx;
}



.unit3{
  width: 90%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin: 30rpx auto;
  //padding: 20rpx;
  //border: silver solid 1px;
  //border-radius: 10px;
  .unit3box{
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-direction: row;
    padding-bottom:5px;
  }
}
</style>
