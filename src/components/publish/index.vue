<template>
<div class="publish-wrap">
  <x-header :left-options="{showBack: false}">
    <a>发布预售</a>
  </x-header>

  <div class="publish">
    <group>
      <selector placeholder="请选择项目类型" title="项目类型" :options="typeList" @on-change="selectType"></selector>
    </group>
    <group>
      <x-input title="众筹金额" keyboard="number" :value.sync="money" placeholder="填写筹款目标金额"></x-input>
    </group>
    <group>
      <cell title="截止日期" >
        <span slot="value">{{rangeDate|getDisAllDate range 'yyyy-MM-dd'}}<b>共{{range}}天</b></span>
      </cell>
      <cell title="选择众筹时间" primary="content">
        <range :value.sync="range" :min="3" :max="30"></range>
      </cell>
    </group>
    <group>
      <switch :value.sync="need_addr" title="需要支持者的收件地址"></switch>
      <x-input v-if="need_addr" :value.sync="exp_money" keyboard="number" title="运费" placeholder="填写运费金额或包邮"></x-input>
      <x-input v-if="need_addr" :value.sync="exp_date" title="发货时间" placeholder="填写发货时间"></x-input>
    </group>
    <group>
      <cell title="项目标签" :value="tags1+' '+tags2+' '+tags3" is-link @click="showPopup=true"></cell>
    </group>
    <popup :show.sync="showPopup" class="checker-popup">
      <div style="padding:10px 10px 40px 10px;">
        <p style="padding: 5px 5px 5px 2px;color:#888;">产品属性</p>
        <checker
        :value.sync="tags1"
        default-item-class="demo4-item"
        selected-item-class="demo4-item-selected"
        disabled-item-class="demo4-item-disabled"
        @on-item-click="showPopup=false">
          <checker-item class="checker-item" value="养生">养生</checker-item>
          <checker-item class="checker-item" value="水果生鲜">水果生鲜</checker-item>
          <checker-item class="checker-item" value="粮油">粮油</checker-item>
          <checker-item class="checker-item" value="私厨">私厨</checker-item>
          <checker-item class="checker-item" value="文创">文创</checker-item>
          <checker-item class="checker-item" value="健康健康">粮油</checker-item>
        </checker>
        <p style="padding: 5px 5px 5px 2px;color:#888;">生产工艺</p>
        <checker
        :value.sync="tags2"
        default-item-class="demo4-item"
        selected-item-class="demo4-item-selected"
        disabled-item-class="demo4-item-disabled"
        @on-item-click="showPopup=false">
          <checker-item class="checker-item" value="天赐美食">天赐美食</checker-item>
          <checker-item class="checker-item" value="良心之作">良心之作</checker-item>
          <checker-item class="checker-item" value="匠人制造">匠人制造</checker-item>
        </checker>
        <p style="padding: 5px 5px 5px 2px;color:#888;">商家标签</p>
        <checker
        :value.sync="tags3"
        default-item-class="demo4-item"
        selected-item-class="demo4-item-selected"
        disabled-item-class="demo4-item-disabled"
        @on-item-click="showPopup=false">
          <checker-item class="checker-item" value="原产地">原产地</checker-item>
          <checker-item class="checker-item" value="自主创业">自主创业</checker-item>
          <checker-item class="checker-item" value="助农">助农</checker-item>
          <checker-item class="checker-item" value="优选良品">优选良品</checker-item>
        </checker>
      </div>
      <p style="padding: 5px 5px 5px 2px;color:#888;">每项选择一种</p>
    </popup>
    <group>
      <x-input :value.sync="title" placeholder="填写你要预售什么产品？"></x-input>
      <x-textarea placeholder="详情介绍下你的产品内容" :value.sync="content" :show-counter="false" :height="200" :rows="8" :cols="30"></x-textarea>
    </group>
    <div class="updata-img">
      <ul class="row">
        <li v-for="url in pics" class="updata-list col-3"><img :src="'http://crowd.iblue.cc/'+url" /></li>
        <li class="col-3 updata-list" v-if="pics.length < 8">
          <div class="updata-icon">
            <i class="icon-plus"></i>
            上传图片</br>(最多8张)
          </div>
          <div class="updata-file" style="opacity:1">
            <form id="uploadForm" action="" method="post" enctype="multipart/form-data">
              <input type="file" v-if="!showPopupPro" v-model="proImg" name="imgFile" @change="change(1)" />
            </form>
          </div>
        </li>
      </ul>
      <div class="help-block">
        请上传清晰的产品图片，建议图片尺寸大于 <strong>750 x 400</strong>
      </div>
    </div>
    <group title="设置回报方式">
      <cell :title="'支持'+item.money+'元'" :inline-desc="item.content" v-for="item in report">
        <img class="pro-min-pic" slot="icon" :src="'http://crowd.iblue.cc/'+item.pics">
        <i @click="reportDel($index)" class="fa fa-close"></i>
      </cell>
    </group>
    <div class="add-pro-wrap" @click="showPopupPro=true">
      <i class="fa fa-plus"></i>添加回报方式
    </div>
    <popup :show.sync="showPopupPro" class="checker-popup">
      <div style="padding:0px 10px 10px 10px;">
        <group>
          <x-input title="支持金额" keyboard="number" :value.sync="reportItem.money" placeholder="填写支持金额(元)"></x-input>
          <x-textarea placeholder="填写回报具体内容" :show-counter="false" :value.sync="reportItem.content" :height="120" :rows="8" :cols="30"></x-textarea>
        </group>
        <div class="popup-pro-pic" v-if="reportItem.pics == ''">
          <div class="updata-icon">
            <i class="icon-plus"></i>
            上传图片
          </div>
          <form id="uploadReport" class="updata-file" action="" method="post" enctype="multipart/form-data">
            <input type="file" v-if="showPopupPro" v-model="proImg" name="imgFile" @change="change(2)" />
          </form>
        </div>
        <div class="report-pic" v-if="reportItem.pics">
          <img v-if="reportItem.pics" :src="'http://crowd.iblue.cc/'+reportItem.pics" />
        </div>
        <group>
          <x-input title="限制数量" :value.sync="reportItem.quantity" keyboard="number" placeholder="默认不限制(份)"></x-input>
        </group>
        <x-button class="popup-btn" @click="reportBtn()" type="primary">添加保存</x-button>
      </div>
    </popup>
    <x-button class="publish-btn" @click="publish()" type="primary">发布项目</x-button>
</div>
<alert :show="!!tipsMsg" title="提示" >{{tipsMsg}}</alert>
</template>

<script>
import { XHeader, Group, XInput, Cell, Range, Checker, CheckerItem, Popup, Switch, XTextarea, XButton, Alert, Selector} from 'vux/src/components'
import upload from 'resource/upload'
import util from '../../utils/dateUtil'
import Api from 'resource/index'
export default{
  components: {
    XHeader, Group, XInput, Cell, Range, Checker, CheckerItem, Popup, Switch, XTextarea, XButton, Alert, Selector
  },
  ready () {
    this.rangeDate = new Date();
    this.categorys();
  },
  data () {
    return {
      type: [],
      typeList: [],
      tipsMsg: '',
      range: 3,
      rangeDate: 0,
      tags1: '',
      tags2: '',
      tags3: '',
      showPopup: false,
      showPopupPro: false,
      pics: [], // 产品图片
      proImg: '', // 上传input
      money: '', //众筹金额
      need_addr: true, // 是否需要收件地址
      exp_money: '', // 运费
      exp_date: '', // 地址
      title: '', // 众筹产品名称
      content: '', // 产品说明
      report: [], //回报
      reportItem: {
        money: '',
        quantity: '',
        pics: '',
        content: ''
      }
    }
  },
  methods: {
    // 上传
    uploadPics: function () {
      let context = this;
      this.upload('uploadForm', function(result) {
        if (result.Code == 0) {
          context.pics.push(result.Result.path);
        } else {
          alert('上传失败！')
        }
        context.proImg = '';
      })
    },
    //上传回报图片
    uploadReport: function() {
      let context = this;
      this.upload('uploadReport', function(result) {
        if (result.Code == 0) {
          context.reportItem.pics = result.Result.path;
        } else {
          alert('上传失败！')
        }
        context.proImg = '';
      })
    },
    // 监听input
    change: function(type) {
      if (this.proImg && type === 1) {
        this.uploadPics()
      } else if (this.proImg && type === 2) {
        this.uploadReport()
      }
    },
    // 删除回报list
    reportDel: function(idx) {
      this.report.splice(idx, 1)
    },
    // 保存回报item
    reportBtn: function() {
      this.report.push(this.reportItem)
      this.reportItem = {
        money: '',
        quantity: '',
        pics: '',
        content: ''
      }
      this.showPopupPro = false;
    },
    // 上传
    upload: function(id, complete) {
      new upload({
        id: id,
        url: '/api/common/upload',
        method: 'POST',
      	onComplete: function(result){
          result = JSON.parse(result);
          complete(result);
      	}
      }).request()
    },
    // 发布
    publish: function() {
      let params = {
        data: {
          type: this.type,
          money: this.money,
          end_date: util.getDisAllDate(this.rangeDate, this.range, 'yyyy-MM-dd'),
          need_addr: this.need_addr ? 1 : 0,
          exp_money: this.exp_money,
          exp_date: this.exp_date,
          tag: this.tags1+','+this.tags2+','+this.tags3,
          title: this.title,
          content: this.content,
          pics: this.pics.join(',')
        },
        report: this.report
      }
      Api.createproject(params).then((response) => {
        let data = JSON.parse(response.body);
        if(data.Status == 'success') {
          alert('发布成功');
          window.location.href = '/#!/raise/info/'+data.Result.id;
        }else {
          alert(data.Message);
        }
      })
    },
    //获取众筹类型
    categorys: function() {
      let context = this;
      Api.categorys().then((response) => {
        let data = JSON.parse(response.body);
        context.typeList = data.Result;
      })
    },
    // 选择项目类型
    selectType: function(val) {
      this.type = val;
    }
  }
}
</script>

<style lang="less">
.publish-wrap{
  .demo4-item {
    background-color: #ddd;
    color: #222;
    font-size: 14px;
    padding: 5px 10px;
    margin-right: 10px;
    line-height: 18px;
    border-radius: 15px;
  }
  .demo4-item-selected {
    background-color: #43ac43;
    color: #fff;
  }
  .demo4-item-disabled {
    color: #999;
  }
  .updata-img{
    border-top: 1px solid #eee;
    border-bottom: 1px solid #eee;
    margin-top: 15px;
    background: #fff;
    padding: 8px 5px 3px;
    .updata-list{
      position: relative;
      min-height:100px;
      img{
        margin: 0 2%;
        vertical-align: top;
        width: 96%;

      }
      .updata-icon{
        line-height: 1.2;
        background: #fff;
        border: 1px dashed #D2D1D6;
        border-radius: 6px;
        color: #D2D1D6;
        padding: 10px 5px;
        font-size: 12px;
        text-align: center;
        display: block;
        .icon-plus{
          background: url(../../assets/img/plus.png) no-repeat;
          background-size: 32px;
          width: 32px;
          height: 32px;
          display: block;
          margin: 5px auto;
        }
      }
      .updata-file{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        opacity: 0;
        z-index: 2;
        input{
          opacity: 0;
          position: absolute;
          top: 0px;
          left: 0px;
          width: 100%;
          height: 100%;
        }
      }
    }
    .help-block{
      line-height: 1.4rem;
      font-size: .7rem;
      text-indent: 3px;
      color:#999;
      padding-top: 8px;
    }
  }
  .pro-min-pic{
    width: 50px;
    margin-right: 10px;
  }
  .add-pro-wrap{
    padding: 12px;
    display: block;
    color: #43AC43;
    font-size: 16px;
    text-align: center;
    background: #fff;
    border-bottom: 1px solid #eee;
  }
  .publish-btn{
    margin-top: 15px;
  }
  .popup-pro-pic{
    position: relative;
    img{
      margin: 0 2%;
      vertical-align: top;
      width: 96%;

    }
    .updata-icon{
      line-height: 1.2;
      background: #fff;
      border: 1px dashed #D2D1D6;
      border-radius: 6px;
      color: #D2D1D6;
      font-size: 12px;
      text-align: center;
      display: block;
      .icon-plus{
        background: url(../../assets/img/plus.png) no-repeat;
        background-size: 32px;
        width: 32px;
        height: 32px;
        display: block;
        margin: 5px auto;
      }
    }
    .updata-file{
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      opacity: 0;
      z-index: 2;
      input{
        opacity: 0;
        position: absolute;
        top: 0px;
        left: 0px;
        width: 100%;
        height: 100%;
      }
    }
  }
  .popup-btn{
    margin-top: 10px;
  }
  .report-pic{
    text-align: center;
    margin-top: 10px;
    background: #fff;
    padding: 5px 0;
    img{
      width: 100px;
      vertical-align: middle;
    }
  }
  .checker-item{margin-bottom: 5px;}
}

</style>
