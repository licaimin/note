<template>
  <div class="home">
    <div class="content">
        <div class="information">
          <div class="userPicture">
            <img :src="materialData.avatar" alt="用户头像">
          </div>
          <span>{{materialData.nickname}}</span>
          <p>{{materialData.personalDescription}}</p>
        </div>
        <b-row class="atten">
          <b-col cols="2" offset="3">
              <span>{{materialData.popularity}}</span>
              <p>人气</p>
          </b-col>
          <b-col cols="2">
            <router-link :to="{path:'/personalHome/focusfans',name:'focusfans',params:{tabs: 'fans'}}" class="personalTag">
              <span>{{materialData.fansCount}}</span>
              <p>粉丝</p>
            </router-link>
          </b-col>
          <b-col cols="2">
            <router-link :to="{path:'/personalHome/focusfans',name:'focusfans',params:{tabs: 'focus'}}" class="personalTag">
              <span>{{materialData.followCount}}</span>
              <p>关注</p>
            </router-link>
          </b-col>
        </b-row>
        <div class="tabBox">
          <ul class="nav nav-pills mb-3 tab" id="pills-tab" role="tablist">
            <li class="nav-item">
              <a class="nav-link active" id="pills-record-tab" data-toggle="pill" href="#pills-record" role="tab" aria-controls="pills-record" aria-selected="true">
                发布记录
                <span></span>
              </a>
            </li>
            <li class="nav-item" style="margin-left: 88px; margin-right: 88px;">
              <a class="nav-link" id="pills-favorite-tab" data-toggle="pill" href="#pills-favorite" role="tab" aria-controls="pills-favorite" aria-selected="false">
                收藏夹
                <span></span>
              </a>
            </li>
            <li class="nav-item">
              <a class="nav-link" id="pills-material-tab" data-toggle="pill" href="#pills-material" role="tab" aria-controls="pills-material" aria-selected="false">
                资料
                <span></span>
              </a>
            </li>
          </ul>
          <div class="tab-content" style="background: #f6f6f6;padding-bottom: 10px;padding-top: 10px;position: relative;top: -1rem;" id="pills-tabContent">
            <!--发布记录-->
            <div class="tab-pane fade show active" style="position: relative;" id="pills-record" role="tabpanel" aria-labelledby="pills-record-tab">
              <!--<datepicker :language="zh" :minimumView="'month'" bootstrap-styling-->
                          <!--:maximumView="'year'" :initialView="'month'"-->
                          <!--v-model="yearmonth" :format="format" class="datepicker"-->
                          <!--calendar-button-icon-content="fa fa-calendar">-->
              <!--</datepicker>-->
              <!--<img src="../../assets/cover/personalCenter/calendar.png" alt="日历icon" class="calendar">-->
              <calendar></calendar>
            </div>
            <!--收藏夹-->
            <div class="tab-pane fade" id="pills-favorite" role="tabpanel" aria-labelledby="pills-favorite-tab">
              <!--类别选择-->
              <ul class="nav nav-pills mb-4 category" role="tablist">
                <li class="nav-item">
                  <a @click="setRecordType('')" class="nav-link active" id="pills-all-tab" data-toggle="pill" href="#pills-all" role="tab" aria-controls="pills-all" aria-selected="true">不限类别</a>
                </li>
                <li class="nav-item">
                  <a @click="setRecordType(2)" class="nav-link" id="pills-work-tab" data-toggle="pill" href="#pills-work" role="tab" aria-controls="pills-work" aria-selected="false">艺术作品</a>
                </li>
                <li class="nav-item">
                  <a @click="setRecordType(3)" class="nav-link" id="pills-photogra-tab" data-toggle="pill" href="#pills-photogra" role="tab" aria-controls="pills-photogra" aria-selected="false">摄影</a>
                </li>
                <li class="nav-item">
                  <a @click="setRecordType(1)" class="nav-link" id="pills-article-tab" data-toggle="pill" href="#pills-article" role="tab" aria-controls="pills-article" aria-selected="false">文章</a>
                </li>
              </ul>
              <!--作品列表-->
              <b-card-group deck class="workList" style="margin: 0 auto;">
                <div v-for="(item,index) in workList">
                  <b-card img-top class="work">
                    <a v-if="viewData.id == '' || materialData.id == viewData.id" href="javascript:;" class="deleteButton" @click="deleteFavorite(index)">×</a>
                    <b-card-img @click="checkWork(item.id)" :src="item.cover" class="authorPicture"></b-card-img>
                    <div class="author">
                      <p  @click="checkWork(item.id)">{{item.title}}</p>
                      <img @click="checkUser(item.userId)" :src="item.avatar" :alt="item.nickname">
                      <strong @click="checkUser(item.userId)">{{item.nickname}}</strong>
                      <span>{{item.postTime}}</span>
                    </div>
                  </b-card>
                </div>
              </b-card-group>
              <b-pagination :total-rows="recordTotal" :per-page="20"
                            v-if="workList != undefined && workList.length !=0"
                                v-model="recordPage" @change="recordData"
                                align="center" hide-goto-end-buttons  />

            </div>
            <!--资料-->
            <!--<div class="tab-pane fade" v-if="materialData.id == viewData.id"-->
                 <!--id="pills-material"-->
                 <!--role="tabpanel" aria-labelledby="pills-material-tab">-->
              <!--<div class="material">-->
                <!--<b-form>-->
                  <!--<div class="programBox">-->
                    <!--<span class="program">头像</span>-->
                    <!--<b-img :src="materialData.avatar" class="headpicture"/>-->
                    <!--<input @change="uploadHeadPic" ref="upload" type="file" accept="image/*"-->
                           <!--capture="camera" name="file" class="headpictureUpload"-->
                            <!--value="修改"/>-->
                    <!--<b-button variant="outline-danger" class="fakeUploadHeadPic" @click="fakeUploadHeadPic">修改</b-button>-->
                  <!--</div>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="昵称" class="program">-->
                    <!--<b-form-input v-model="materialData.nickname" placeholder="不超过8个汉字" class="input"></b-form-input>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="手机号" class="program">-->
                    <!--<b-form-input v-model="materialData.mobile" class="input"></b-form-input>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--label="性别"-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md" class="program">-->
                    <!--<b-form-radio-group v-model="materialData.sex"-->
                                        <!--:options="options"-->
                                        <!--name="radioInline" style="padding-top: 8px;">-->
                    <!--</b-form-radio-group>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="个性签名" class="program">-->
                    <!--<b-form-input v-model="materialData.personalDescription"></b-form-input>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="地区" class="program">-->
                  <!--<v-distpicker @selected="onSelected" :placeholders="placeholder"-->
                                <!--:province="country"-->
                                <!--:city="province"-->
                                <!--:area="city"-->
                                <!--class="datepicker"></v-distpicker>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="生日" class="program">-->
                    <!--<select class="yearselect" v-model="materialData.birthdayYear">-->
                      <!--<option v-for="item in yearoptions" :value="item">{{item}}</option>-->
                    <!--</select>-->
                    <!--<select class="monthselect" v-model="materialData.birthdayMonth" @change="birthdayGet">-->
                      <!--<option v-for="item in monthoptions" :value="item">{{item}}</option>-->
                    <!--</select>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="职业" class="program">-->
                    <!--<select class="professionselect" v-model="materialData.profession">-->
                      <!--<option v-for="item in professionGroup" :value="item.key">{{item.value}}</option>-->
                    <!--</select>-->
                  <!--</b-form-group>-->

                  <!--<b-form-group horizontal-->
                                <!--:label-cols="1"-->
                                <!--breakpoint="md"-->
                                <!--label="星座" class="program">-->
                    <!--<select class="constellationselect" v-model="materialData.constellation">-->
                      <!--<option v-for="item in constellationGroup" :value="item.key">{{item.value}}</option>-->
                    <!--</select>-->
                  <!--</b-form-group>-->
                  <!--<div style="text-align: center">-->
                    <!--<b-button variant="primary" style="width: 200px;" @click="saveMaterial">保存</b-button>-->
                  <!--</div>-->
                <!--</b-form>-->
              <!--</div>-->
            <!--</div>-->
            <!--访客-->
            <div class="tab-pane fade viewBox"
                 id="pills-material" 
                 role="tabpanel" aria-labelledby="pills-material-tab">
              <b-row class="row">
                <b-col cols="2" class="title">昵称</b-col>
                <b-col class="data">{{viewData.nickname}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2"class="title">手机号码</b-col>
                <b-col class="data">{{viewData.mobile}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2"class="title">性别</b-col>
                <b-col class="data">{{viewData.sex}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2" class="title">个人签名</b-col>
                <b-col class="data">{{viewData.personalDescription}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2" class="title">地区</b-col>
                <b-col class="data">{{viewData.country+ viewData.province+viewData.city}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2" class="title">生日</b-col>
                <b-col class="data">{{viewData.birthday}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2" class="title">职业</b-col>
                <b-col class="data">{{viewData.profession}}</b-col>
              </b-row>
              <b-row class="row">
                <b-col cols="2" class="title">星座</b-col>
                <b-col class="data">{{viewData.constellation}}</b-col>
              </b-row>
            </div>
          </div>
        </div>
        <b-modal hide-header hide-footer ref="deleteFavorite">删除收藏失败</b-modal>
        <b-modal hide-header hide-footer ref="changeSuccess">修改个人资料成功</b-modal>
        <b-modal hide-header hide-footer ref="changeFailed">修改个人资料失败</b-modal>
    </div>
  </div>
</template>

<script>
  import VDistpicker from 'v-distpicker'
  import Datepicker from 'vuejs-datepicker';
  import {zh} from 'vuejs-datepicker/dist/locale'
  import calendar from './calendar'

  export default {
    name: "record",
    components: { VDistpicker ,Datepicker,calendar},
    data: function () {
      return {
        // 访客数据
        viewData:{
          nickname: '',
          sex: '',
          mobile: '',
          birthday : '',
          avatar: '',
          country: '',
          province: '',
          city:'',
          profession: '',
          fansCount:'',
          followCount: '',
          popularity:'',
          constellation: ''
        },
        userPicture: "https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg",
        userName:"山河湖海天地",
        slogan:"这个人很懒，什么都没有写…撒大撒",
        popular: 5100,
        fan: 36,
        focus: 52,
        tabs: 'record',
        //发布记录
        zh: zh,
        yearmonth: '2019-02',
        format: 'yyyy月 MM日',
        //收藏夹
        recordPage: 1,
        recordTotal: 20,
        recordType:'',
        workList:[{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '1111',
          userId: '2'
        },{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '2',
          userId: '2'
        },{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '3',
          userId: '2'
        },{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '4',
          userId: '2'
        },{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '5',
          userId: '2'
        },{
          title:'嘻嘻嘻',
          cover: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          nickname: '天上有个云',
          postTime:'五分钟前',
          workId: '6'
        }],
        //资料
        headPicture: null,
        materialData:{
          id:'',
          avatar: '',
        },
        placeholder:{province: ' 省份 ',city: ' 市 ',area: ' 区 '},
        options: [
          { text: '男', value: '1' },
          { text: '女', value: '0' },
          { text: '保密', value: '2' }
        ],
        uploadData:{
          avatar: '',
          name: '',
          phone: '',
          sex : '',
          slogan: '',
          area: '',
          birthday: '',
          occupation: '',
          constellation: ''
        },
        yearoptions: ['年',2019,2018,2017,2016,2015,2014,2013,2012,2011,2010,2009,2008,2007,2006,2005,2004,2003,2002,2001,
        2000,1999,1998,1997,1996,1995,1994,1993,1992,1991,1990,1989,1988,1987,1986,1985,1984,1983,1982,1981,1980,1979,
          1979,1978,1977,1976,1975,1974,1973,1972,1971,1970,1969,1968,1967,1966,1965,1964,1963,1962,1961,1960,1959,
          1958,1957,1956,1955],
        monthoptions: ['月','01','02','03','04','05','06','07','08','09','10','11','12'],
        birthdayYear: '年',
        birthdayMonth: '月',
        profession: null,
        professionGroup: [{
          key: null,
          value :"请选择职业"
        },{
          key:"学生",
          value :"学生"
        },{
          key:"计算机/互联网/通信/电子",
        value:"计算机/互联网/通信/电子"
        },{
          key:"会计/金融/银行/保险",
          value:"会计/金融/银行/保险"
        },{
          key:"贸易/消费/制造/营运",
          value:"贸易/消费/制造/营运"
        },{
          key:"制药/医疗",
          value:"制药/医疗"
        },{
          key:"广告/媒体",
          value:"广告/媒体"
        },{
          key:"房地产/建筑",
          value:"房地产/建筑"
        },{
          key:"专业服务/教育/培训",
          value:"专业服务/教育/培训"
        },{
          key:"服务业",
          value:"服务业"
        },{
          key:"物流/运输",
          value:"物流/运输"
        },{
          key:"能源/原材料",
          value:"能源/原材料"
        },{
          key:"政府/非营利性组织/其他",
          value:"政府/非营利性组织/其他"
        }],
        constellation: null,
        constellationGroup: [{
          key: null,
          value: "请选择星座",
        },{
          key: "魔羯座",
          value: "魔羯座",
        },{
          key: "水瓶座",
          value: "水瓶座",
        },{
          key: "双鱼座",
          value: "双鱼座",
        },{
          key: "白羊座",
          value: "白羊座",
        },{
          key: "金牛座",
          value: "金牛座",
        },{
          key: "双子座",
          value: "双子座",
        },{
          key: "巨蟹座",
          value: "巨蟹座",
        },{
          key: "狮子座",
          value: "狮子座",
        },{
          key: "处女座",
          value: "处女座",
        },{
          key: "天秤座",
          value: "天秤座",
        },{
          key: "天蝎座",
          value: "天蝎座",
        },{
          key: "射手座",
          value: "射手座",
        }]
      }
    },
    methods:{
      // 收藏夹
      deleteFavorite: function (index) {
        let that = this;
        this.$axios({
          method: 'del',
          baseURL:'/api/favourite',
          url: '/' + index,
        }).then((res)=>{
          console.log(res.data.data);
          this.workList.splice(index, 1);
        }).catch((error)=>{
          this.$refs.deleteFavorite.show();
          console.log(error);
        })
      },
      // 查询作品
      checkWork:function(id){
        console.log(id);
      },
      //查看用户
      checkUser:function(userId){
        this.$router.push({name: 'profile', query:{userId: userId}});
      },
      onSelected:function (data) {
        this.materialData.country = data.province.value;
        this.materialData.province = data.city.value;
        this.materialData.city = data.area.value;
        this.country = data.province.value;
        this.province = data.city.value;
        this.city = data.area.value;
      },
      birthdayGet: function () {
        this.materialData.birthday = this.birthdayYear + this.birthdayMonth;
      },
      setRecordType:function(type){
        this.recordType = type;
        this.recordData();
      },
      recordData(){
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/favourite',
          params:{
            type: that.recordType,
            pageNum: that.recordPage,
            pageSize: 20
          }
        }).then((res)=>{
          that.workList = res.data.data.rows;
          that.recordTotal = res.data.data.total;
        }).catch((error)=>{
          console.log(error);
        })
      },
      // 资料
      fakeUploadHeadPic:function(){
        this.$refs.upload.click();
      },
      uploadHeadPic:function(){
        let formData = new FormData();
        formData.append('file', this.$refs.upload.files[0]);
        let that = this;
        this.$axios({
          method: 'post',
          baseURL: '/api/qiniu/upload',
          anync:true,
          contentType:false,
          processData:false,
          data:formData
        }).then((res)=>{
          that.materialData.avatar = res.data.data;
        }).catch((error)=>{
          console.log(error);
        })
      },
      materialDataGet:function () {
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/profile',
          url: '/' + that.materialData.id,
        }).then((res)=>{
          that.materialData = res.data.data;
          that.materialData.birthdayYear = res.data.data.birthday.substr(0,4);
          that.materialData.birthdayMonth = res.data.data.birthday.substr(5,2);
          that.country = res.data.data.country;
          that.province = res.data.data.province;
          that.area = res.data.data.area;
        }).catch((error)=>{
          console.log(error);
        })
      },
      saveMaterial:function () {
        let that = this;
        this.$axios({
          method: 'put',
          baseURL: '/api/user',
          data: that.materialData
        }).then((res)=>{
          that.$refs.changeSuccess.show();
        }).catch((err)=>{
          that.$refs.changeFailed.show();
        })
      },
      viewDataGet:function () {
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/profile',
          url: '/' + that.viewData.id,
        }).then((res)=>{
          that.viewData = res.data.data;
          that.viewData.birthdayYear = res.data.data.birthday.substr(0,4);
          that.viewData.birthdayMonth = res.data.data.birthday.substr(5,2);
          that.country = res.data.data.country;
          that.province = res.data.data.province;
          that.area = res.data.data.area;
        }).catch((error)=>{
          console.log(error);
        })
      }
    },
    created:function () {
      let that = this;
      this.recordData();
      // 取得当前用户id
      var userData=JSON.parse(this.$store.state.userData)
      this.materialData.avatar=userData.avatar
      this.materialData.id =userData.id
      this.viewData.id = this.$route.query.id;
      if (this.viewData.id != undefined){
        this.viewDataGet();
      }
    }
  }
</script>

<style scoped>
  .home{
      padding: 0;
      background-color: #f5f5f5;
  }
  .content{
    overflow: hidden;
    zoom: 1;
    font-size: 16px;
    width: 1136px;
    margin: 0 auto;
    background-color: #fff;
  }
  .information{
    margin-top: 64px;
    text-align: center;
  }
  .userPicture{
    overflow: hidden;
    margin: auto auto;
    width: 120px;
    height: 120px;
    text-align: center;
    border-radius: 50%;
  }
  .userPicture img{
    width: 100%;
  }
  .information span{
    display: inline-block;
    font-size: 1.5rem;
    line-height: 24px;
    margin-top: 24px;
    margin-bottom: 16px;
  }
  .information p{
    font-size: 16px;
  }
  .atten{
    margin-top: 64px;
    text-align: center;
  }
  .tabBox{
    background: ##F6F6F6;
    margin-top: 48px;
  }
  .tabBox .tab{
    text-align: center;
    margin: 0 auto;
    margin-bottom: 0;
    width: 370px;
  }
  .tabBox .tab a{
    width: 64px;
    display: inline-block;
    padding: 0;
    font-size: 16px;
    color: #999999;
  }
  .tab span{
    margin: 0 auto;
    display:block;
    height: 4px;
    width:32px;
    background-color: inherit;
    margin-top: 16px;
  }
  .tab-pane{
    width: 80%;
    margin: 0 auto;
  }
  .tabBox .tab .active{
    background: inherit;
    border-right: 0;
    color: #515151;
    font-weight: bold;
    /*border-bottom: 4px solid #4959F6;*/
  }
  .tabBox .nav .active span{
    background-color: #4959F6;
  }
  .category{
    padding-left: 45px;
  }
  .category .nav-link{
    width: 88px;
    height: 32px;
    font-size: 14px;
    line-height: 14px;
    font-weight: bold;
    text-align: center;
    color: #515151;
  }
  .category .active{
    color: #fff;
  }
  /*发布记录*/
  .datepicker{
    height: 32px;
    /*width: 160px;*/
    border-radius: 10px;
  }
  .calendar{
    width: 16px;
    height: 16px;
    position: absolute;
    top:11px;
    left: 130px;
  }
  .personalTag{
    text-decoration: none;
  }
  /*收藏夹*/
  .workList{
    padding-left: 30px;
  }
  .work{
    margin-bottom: 24px;
    width: 90%;
    height: 234px;
    border: none;
    overflow: hidden;
  }
  .work div{
    padding: 0;
  }
  .work p{
    margin-top: 16px;
    font-size: 14px;
    line-height: 16px;
    color: #515151;
  }
  .authorPicture{
    cursor: pointer;
    width: 100%;
    height: 148px;
    border-radius: 0;
  }
  .author{
    margin: 0 16px;
  }
  .author p{
    cursor: pointer;
  }
  .author img{
    cursor: pointer;
    width: 24px;
    height: 24px;
    border-radius: 50%;
  }
  .author strong{
    cursor: pointer;
    font-size: 12px;
    line-height: 12px;
    font-weight: normal;
    margin-left: 16px;
  }
  .author span{
    position: absolute;
    right: 11px;
    bottom: 18px;
    line-height: 12px;
    font-size: 12px;
    color: #C1C1C1;
  }
  .deleteButton{
    visibility: hidden;
    position: absolute;
    top: 8px;
    right: 8px;
    width: 24px;
    height: 24px;
    text-align: center;
    font-size: 25px;
    line-height: 20px;
    border-radius: 4px;
    background: rgba(0,0,0,0.5);
    text-decoration: none;
    color: #fff;
  }
  .work:hover .deleteButton{
    visibility: visible;
  }
  .programBox{
    position: relative;
    margin-bottom: 15px;
  }
  .material{
    padding-left: 100px;
    padding-bottom: 50px;
  }
  .material .headpicture{
    display: inline;
    margin-left: 10px;
    width: 64px;
    height: 64px;
    border-radius: 50%;
  }
  .program{
    margin-right: 42px;
    padding: 0;
  }
  .headpictureUpload{
    display: none;
  }
  .fakeUploadHeadPic{
    margin-left: 10px;
    border: none;
    font-size: 14px;
  }
  .input{
    width: 216px;
    background: rgba(246,246,246,1);
  }
  .yearselect, .monthselect, .professionselect, .constellationselect{
    padding-left: 13px;
    width: 136px;
    height: 40px;
    border-radius: 5px;
  }
  .professionselect{
    width: 272px;
  }
  /*访客资料*/
  .viewBox{
    width: 60%;
    border-radius: 10px;
    background: #fff;
    padding-top: 10px;
    padding-bottom: 22px;
  }
  .viewBox .row{
    margin-top: 22px;
  }
  .viewBox .title{
    color: #999999;
    font-size: 14px;
    text-align: right;
  }
  .viewBox .data{
    margin-left: 20px;
    color: #515151;
    font-size: 14px;
  }
</style>
