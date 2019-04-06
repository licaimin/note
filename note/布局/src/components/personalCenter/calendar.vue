<template>
  <div class="calendar-box">
    <div class="month-container">
      <input type="text" v-model='msg' @click='display' class="calendarShow" placeholder="请选择时间" readonly>
      <div class="month-box" :class="{'date-hide' : hideFlag}">
        <header>
          <span class="pre-month" @click='preYear'></span>
          {{sYear}} 年
          <span class="next-month" @click='nextYear'></span>
        </header>
        <main>
          <ul>
            <li v-for="item in monthRange" @click="chooseMonth(item)">{{item}}月</li>
          </ul>
        </main>
      </div>
    </div>

    <table class="date-box">
      <thead>
      <tr>
        <th v-for='weekName in weeks'
            :class="'red'">{{weekName}}</th>
      </tr>
      </thead>
      <tbody>
      <template>
        <tr v-for='week in daysData'>
          <td v-for='dayObj in week'
              :class="[dayObj.active ? 'active-date':'unactive-date',
								{'choose-date' : dayObj.day == sDay && dayObj.active,
								'red' : dayObj.day != sDay && dayObj.active}]">
            <div :class="{'unactive-work': !dayObj.isActive}" v-if="!dayObj.isActive">
              {{dayObj.day}}
            </div>
            <div :class="{'active-work': dayObj.isActive}" v-if="dayObj.isActive">
              <i>{{dayObj.day}}</i>
              <div class="workBox" style="height: 112px;width: 120px;border-radius: 10px;overflow: hidden;margin: 0 auto;">
                <div class="imgBox" style="height: 80px;width: 100%;">
                  <img :src="dayObj.workList[0].cover" alt="是" style="width: 100%;height: 100%;">
                </div>
                <div class="inforBox" style="height: 32px; font-size: 14px;background: #fff;line-height: 32px; ">
                  <p style="position: relative;height: 32px;color: blue;text-align: left;padding-left: 10px;">
                    {{dayObj.workList[0].title}}
                    <img style="position:absolute; right: 10px; top: 8px;" @click.stop="showDelete(dayObj.workList[0].id)" src="../../assets/picture/personalCenter/delete.png" alt="删除">
                  </p>
                </div>
              </div>
              <span unselectable='on' @click="showList(dayObj)" v-if="dayObj.workList != undefined && dayObj.workList.length >= 2">...</span>

            </div>
            <div class="workList" :class="{listShow:dayObj.listFlag}" v-if="dayObj.workList != undefined && dayObj.workList.length !=0">
              <template v-for="item in dayObj.workList">
                <div class="workBox" style="margin-top: 10px;float:left; display:inline-block;margin-left: 8px;margin-right: 10px;">
                  <div class="imgBox" style="height: 80px;width: 100%;">
                    <img :src="item.cover" alt="是" style="width: 100%;height: 100%;">
                  </div>
                  <div class="inforBox" style="position: relative;height: 32px; font-size: 14px;background: #fff;line-height: 32px; ">
                    <p style="height: 32px;color: blue;text-align: left;padding-left: 10px;">
                      {{item.title}}
                      <img style="position:absolute; right: 10px; top: 8px;" @click="showDelete(item.id, dayObj.workList)" src="../../assets/picture/personalCenter/delete.png" alt="删除">
                    </p>
                  </div>
                </div>
              </template>
            </div>
          </td>
        </tr>
      </template>
      </tbody>
    </table>
    <b-modal @ok="deleteWork" hide-header ok-title="确定" cancel-title="取消" ref="deleteModal">确认删除此作品</b-modal>
  </div>
</template>

<script>
  module.exports = {
    props: {
      //配置
      //回调函数
      callback: {
        type: Function,
        default: Function,
      },
    },

    data: function() {
      var now = new Date();
      var y = now.getFullYear(),
        m = now.getMonth() + 1,
        d = now.getDate();
      //返回基本数据
      return {
        deleteId: '',
        deleteList: [],
        //作品列表
        workList:[],
        weeks: ['日', '一', '二', '三', '四', '五', '六'],
        //选择的日期
        sYear: y,
        sMonth: m,
        sDay: d,
        monthRange: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
        yearRange: this.initYearRange(y),
        daysData: this.creatDate(y, m),
        msg: '',
        hideFlag: true,
        //日期段时的前后日期
        nYear: '',
        nMonth: '',
        nDay: '',
        pYear: '',
        pMonth: '',
        pDay: '',
      }
    },
    methods: {
      showList:function(obj){
        obj.listFlag = !obj.listFlag;
        console.log(obj);
      },
      //初始化年的范围
      initYearRange: function(y, num) {
        var yearRange = [],
          num = parseInt(num) || 20;
        for (var i = -num; i < num; i++) {
          yearRange.push(y + i);
        }
        return yearRange;
      },
      //根据年月生成日历数据
      creatDate: function(year, month) {
        this.getData(year, month);
        //一个月多少周和一周多少天
        var WEEK_NUM = 6,
          DAY_NUM = 7,
          //按日历格式放日历数据为一个对象
          daysData = {},
          showDate = new Date(year, month - 1),
          //日子标志
          daysFlag = 1 - showDate.getDay();
        let workList = this.workList;
        let currentWork = [];
        if (workList != undefined){
          for (let k=0; k<workList.length; k++){
            let date = new Date(workList[k].publishTime.replace(/-/g, '/'));
            let day = date.getDate();
            currentWork.push({id: workList[k].id,day: day, title: workList[k].title, cover: workList[k].cover});
          }
          // console.log(currentWork);
        }
        for (var i = 0; i < WEEK_NUM; i++) {
          daysData[i] = [];
          for (var j = 0; j < DAY_NUM; j++) {
            daysData[i][j] = {};
            showDate = new Date(year, month - 1, daysFlag);
            //相等则直接添加
            if (showDate.getDate() == daysFlag) {
              for (let k=0; k<currentWork.length; k++){
                if (currentWork[k].day == daysFlag){
                  if(daysData[i][j].isActive != true){
                    daysData[i][j].listFlag = true;
                    daysData[i][j].workList = [];
                  }
                  daysData[i][j].workList.push({
                    id: currentWork[k].id,
                    cover: currentWork[k].cover,
                    title: currentWork[k].title.length > 5 ? currentWork[k].title.substr(0,5) + '...':currentWork[k].title
                  });
                  daysData[i][j].id = currentWork[k].id;
                  daysData[i][j].cover = currentWork[k].cover;
                  daysData[i][j].title = currentWork[k].title.length > 5 ? currentWork[k].title.substr(0,5) + '...':currentWork[k].title;
                  daysData[i][j].isActive = true;
                }
              }
              daysData[i][j].day = daysFlag;
              daysData[i][j].active = true;
            } else {
              //判断5行还是6行，如果5行则退出循环
              if (i == 5 && j == 0 && showDate.getDate() < 9) {
                delete daysData[i];
                break;
              }
              //否则将标志转换为标准日期再添加
              daysData[i][j].day = showDate.getDate();
              daysData[i][j].active = false;
            }
            //更新日期
            daysFlag++;
          }
        }
        return daysData;
      },
      //格式化日期
      formatDate: function(y, month, day) {
        if (!y || !month || !day) {
          return '';
        }
        var m = month < 10 ? '0' + month : month,
          d = day < 10 ? '0' + day : day;
        return y + '年' + m + '月';
      },
      //显示选择那一天
      showDate: function() {
        return this.formatDate(this.sYear, this.sMonth, this.sDay);
      },
      //更新日历数据
      update: function() {
        var day = new Date(this.sYear, this.sMonth, 0),
          lastDay = day.getDate();
        this.sDay = lastDay < this.sDay ? lastDay : this.sDay;
        this.daysData = this.creatDate(this.sYear, this.sMonth);
        this.msg = this.sYear + "年 " + this.sMonth + "月";
      },
      //上一月日历数据
      preYear: function() {
        // if (this.sMonth == 1) {
        //   this.sMonth = 12;
        //   this.sYear--;
        // } else {
        //   this.sMonth--;
        // }
        this.sYear--;
        // this.update();
      },
      //下一月日历数据
      nextYear: function() {
        // if (this.sMonth == 12) {
        //   this.sMonth = 1;
        //   this.sYear++;
        // } else {
        //   this.sMonth++;
        // }
        this.sYear++;
        // this.update();
      },
      //选择月份
      chooseMonth:function(month){
        this.sMonth = month;
        this.hideFlag = !this.hideFlag;
        this.update();
      },
      //选择日期
      chooseDay: function(dayObj) {
        if (!dayObj.active) {
          return;
        }
        this.sDay = dayObj.day;
        this.hideFlag = true;
        this.msg = this.showDate();
        alert("这是回调，你选择的是 " + this.showDate());
        this.callback && this.callback();
      },
      //显示与隐藏日历面板
      display: function() {
        this.hideFlag = !this.hideFlag;
      },
      //选择一段日期
      chooseInterval: function(dayObj) {
        if (!dayObj.active) {
          return;
        }
        if (!this.pYear) {
          //第一次选择，记录好日期
          this.pYear = this.sYear;
          this.pMonth = this.sMonth;
          this.pDay = dayObj.day;
        } else if (this.pYear == this.sYear && this.pMonth == this.sMonth &&
          this.pDay == dayObj.day) {
          //重复选择则重置
          this.pYear = '';
          this.pMonth = '';
          this.pDay = '';
          this.nYear = '';
          this.nMonth = '';
          this.nDay = '';
        } else {
          //第二次选择
          this.nYear = this.sYear;
          this.nMonth = this.sMonth;
          this.nDay = dayObj.day;
        }
        this.msg = this.showInterval();
      },
      //显示一段日期
      showInterval: function() {
        var startDate = this.formatDate(this.pYear, this.pMonth, this.pDay),
          endDate = this.formatDate(this.nYear, this.nMonth, this.nDay);
        if (!startDate) {
          return "";
          //判断哪个大
        } else if (Date.parse(startDate) > Date.parse(endDate)) {
          return endDate + " 至 " + startDate;
        } else {
          return startDate + " 至 " + endDate;
        }
      },
      showDelete:function(id, workList){
        this.deleteId = id;
        this.deleteList = workList;
        this.$refs.deleteModal.show();
      },
      deleteWork: function(){
        let deleteId = this.deleteId;
        let workList = this.deleteList;

        for (let i=0; i<workList.length; i++){
          if (workList[i].id == deleteId){
            workList.splice(i,1);
            break;
          }
        }
      },
      //确认选择的日期
      confirm: function() {
        if (!this.pYear || !this.nYear) {
          alert('请选择日期');
          return;
        } else {
          //相减，确认日期在范围内
          var startDate = this.formatDate(this.pYear, this.pMonth, this.pDay),
            endDate = this.formatDate(this.nYear, this.nMonth, this.nDay),
            diff = Math.abs((Date.parse(startDate) - Date.parse(endDate)) / (24 * 60 * 60 * 1000));
          this.hideFlag = true;
          alert("这是回调，你选择的是 " + this.showInterval());
          this.callback && this.callback();
        }
      },
      //取消
      cancel: function() {
        this.msg = '';
        this.pYear = '';
        this.pMonth = '';
        this.pDay = '';
        this.nYear = '';
        this.nMonth = '';
        this.nDay = '';
        this.hideFlag = !this.hideFlag;
      },
      getData:function (sYear,sMonth) {
        if (!sYear || !sMonth){
          return;
        }
        let that = this;
        this.$axios({
          method:'get',
          baseURL:'/api/profile/publishRecord',
          params:{
            year: sYear,
            month: sMonth
          },
          headers:{
            Authorization:"Bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiIxMDk1Njc2ODk3NzM3NTA2ODE2Iiwic3ViIjoidGVzdDAwMDciLCJpYXQiOjE1NTA5NzY3MjUsInJvbGVzIjoidXNlciIsImV4cCI6MTU1MTIzNTkyNX0.iqriTTjXIa9T31KfBA_sqqwHUfr5AQMI8-hBIBhhrOw"
          },
        })
          .then((response) =>{
            that.workList = response.data.data;
          })
          .catch(function (error) {
            console.log(error);
          });

      }
    },
    created(){
      this.getData();
    },
    // computed:{
    //   daysData: function() {
    //     let now = new Date();
    //     let year = this.sYear,
    //       month = this.s;
    //     //一个月多少周和一周多少天
    //     var WEEK_NUM = 6,
    //       DAY_NUM = 7,
    //       //按日历格式放日历数据为一个对象
    //       daysData = {},
    //       showDate = new Date(year, month - 1),
    //       //日子标志
    //       daysFlag = 1 - showDate.getDay();
    //     for (var i = 0; i < WEEK_NUM; i++) {
    //       daysData[i] = [];
    //       for (var j = 0; j < DAY_NUM; j++) {
    //         daysData[i][j] = {};
    //         showDate = new Date(year, month - 1, daysFlag);
    //         //相等则直接添加
    //         if (showDate.getDate() == daysFlag) {
    //           daysData[i][j].day = daysFlag;
    //           daysData[i][j].active = true;
    //         } else {
    //           //判断5行还是6行，如果5行则退出循环
    //           if (i == 5 && j == 0 && showDate.getDate() < 9) {
    //             delete daysData[i];
    //             break;
    //           }
    //           //否则将标志转换为标准日期再添加
    //           daysData[i][j].day = showDate.getDate();
    //           daysData[i][j].active = false;
    //         }
    //         //更新日期
    //         daysFlag++;
    //       }
    //     }
    //     return daysData;
    //   }
    // }
  }
</script>

<style scoped>
  ul{
    margin: 0;
  }
  .calendar-box{
    margin: 0 auto;
    font-family: "Microsoft Yahei";
    width: 100%;
  }
  .calendarShow{
    width: 150px;
    padding-left:15px;
    outline: none;
    line-height: 30px;
    border: none;
    border-radius: 6px;
  }
  .month-container{
    position: relative;
    margin-left: 45px;
  }
  .month-box{
    padding:8px;
    padding-top:0;
    position: absolute;
    top: 35px;
    text-align: center;
    background-color: #fff;
    line-height: 40px;
    width: 270px;
    border-radius: 10px;
  }
  .month-box header{
    border-bottom: 1px solid #ccc;
  }
  .month-box span{
    line-height: 40px;
    cursor: pointer;
  }
  .month-box select{
    cursor: pointer;
    color: #fff;
    margin:0 5px;
    font-family: "Microsoft Yahei";
    outline: none;
    font-size: 16px;
    background-color: transparent;
    border: 0;
    appearance: none;
    -moz-appearance: none;
    -webkit-appearance: none;
  }
  .month-box select:hover{
    color: #000;
  }
  .month-box main li{
    cursor: pointer;
    display: inline-block;
    width: 33.3%;
    height: 30px;
    line-height: 30px;
    text-align: center;
  }
  .month-box main li:hover{
    background: #4959F6;
    color: #fff;
  }
  .date-hide{
    display: none;
  }
  .date-box{
    border-collapse:separate;
    border-spacing:0px 10px;
    margin: 0 auto;
    margin-top: 20px;
    padding: 0;
    text-align: center;
    padding-bottom: 150px;
  }
  .date-box th{
    line-height: 80px;
    margin-right: 56px;
    width: 170px;
    height: 112px;
  }
  .date-box td{
    line-height: 80px;
    margin-right: 56px;
    width: 14%;
    height: 112px;
  }
  .workBox{
    cursor: pointer;
    height: 112px;
    width: 120px;
    border-radius: 10px;
    overflow: hidden;
    margin: 0 auto;
  }
  .active-work{
    position: relative;
  }
  .active-work i{
    position: absolute;
    left:0;
    top: 0;
    display: inline-block;
    width: 24px;
    height: 24px;
    border-radius: 50%;
    font-size: 16px;
    line-height: 24px;
    text-align: center;
    background: #4959F6;
    color: #fff;
  }
  .active-work .work{
    width: 120px;
    height: 112px;
  }
  .hidden{
    display: none;
  }
  .active-work span{
    cursor: pointer;
    display: inline-block;
    position: absolute;
    bottom: 0;
    right: 5px;
    width: 16px;
    height: 16px;
    font-weight: bold;
    background: #fff;
    line-height: 8px;
    border-radius: 2px;
    z-index: 999;
    -webkit-user-select:none;
    -moz-user-select:none;
    -ms-user-select:none;
    user-select:none;
  }
  .unactive-work{
    position: relative;
    top: 15px;
    display: inline-block;
    background: #fff;
    width: 80px;
    height: 80px;
    border-radius: 50%;
  }
  .choose-date{
  }
  .unactive-date{
    visibility: hidden;
  }
  .red{
    color: #515151;
  }
  .next-month{
    margin:12px 10px 0 0;
    border-top:8px solid transparent;
    border-bottom:8px solid transparent;
    border-left:12px solid #888;
    float: right;
  }
  .pre-month{
    margin:12px 0 0 10px;
    border-top:8px solid transparent;
    border-bottom:8px solid transparent;
    border-right:12px solid #888;
    float: left;
  }
  .workList{
    display: block;
    overflow: hidden;
    text-align: center;
    /*width: 170%;*/
    /*height: 130%;*/
    background: #F6F6F6;
    padding: 16px;
    border-radius: 5px;
    position: absolute;
    /*right: -50%;*/
    /*top: 112px;*/
    box-shadow: 0 0 3px #888;
    z-index: 999;
  }
  .listShow{
    display: none;
  }
</style>
