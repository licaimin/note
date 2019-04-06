<template>
  <div class="home">
      <div class="content">
        <b-row>
          <b-col offset="1" cols="8" class="content left">
            <div class="part1">
              <b-row>
                <b-col>
                  <p class="alert">作品尚未发布，预览中</p>
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                  <h1>{{title}}</h1>
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                  <p class="time">...前发布</p>
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                  <span v-for="item in tag" class="tag">{{item.value}} / </span>
                </b-col>
              </b-row>
              <div class="iconList">
                <p class="iconBox">
                  <img src="../../assets/picture/personalCenter/num.png" alt="浏览量">
                  <span>0</span>
                </p>
                <p class="iconBox">
                  <img src="../../assets/picture/personalCenter/load.png" alt="浏览量">
                  <span>0</span>
                </p>
                <p class="iconBox">
                  <img src="../../assets/picture/personalCenter/like.png" alt="浏览量">
                  <span>0</span>
                </p>
              </div>
            </div>
            <div class="part2">
              <b-row>
                <b-col>
                  <p class="description">
                    {{description}}
                  </p>
                </b-col>
              </b-row>
            </div>
            <div class="part3">
              <b-row>
                <b-col v-html="content">{{content}}</b-col>
              </b-row>
            </div>
          </b-col>

          <b-col cols="2" class="container right">
            <div class="headPicture">
              <img :src="headPicture" alt="头像">
            </div>
            <div class="information">
              <h2 class="name">{{name}}</h2>
              <p class="slogan">{{slogan}}</p>
            </div>
            <div class="status">
              <p class="statusBox">
                <img src="../../assets/picture/personalCenter/like-user.png" alt="点赞数">
                <span>0</span>
              </p>
              <p class="statusBox">
                <img src="../../assets/picture/personalCenter/load-user.png" alt="收藏数">
                <span>0</span>
              </p>
            </div>
          </b-col>
        </b-row>
        <b-row>
          <b-col offset="1" cols="8" class="container">
            <p class="footer">正在预览无法评论</p>
          </b-col>
        </b-row>
        <div class="back">
          <b-button variant="outline-primary" @click="back" style="width: 100px;">
            返回
          </b-button>
        </div>
      </div>
  </div>
</template>

<script>
    export default {
        name: "preview",
      data:function () {
        return {
          name: '囿于山河湖海...',
          slogan: '很久没发作品了，不过下半年应该',
          headPicture:'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
          title:'人子X',
          tag: ['原创作品','插画','手绘'],
          description: '“所谓漫才啊，就是不管他是要卖鱼还是卖菜，都是漫才！”——「神谷」。总结之前练习的一些东西和感悟，作为自己的笔记吧！',
          content: ''
        }
      },
      methods:{
        back:function () {
          this.$router.go(-1);
        }
      },
      created(){
        let data = this.$route.params.workData;
        if (data == undefined){
          this.$router.go(-1);
          return false;
        }
        this.title = data.title;
        this.tag = data.tag;
        this.description = data.description;
        this.content = data.content;
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
  .part1{
    position: relative;
    padding-bottom: 24px;
    border-bottom: 1px solid #EEEEEE;
    margin-bottom: 39px;
  }
  .alert{
    font-size: 14px;
    color: #FF6E6E;
    padding: 0;
    border: none;
    font-weight: bold;
  }
  h1{
    font-size: 24px;
    margin-bottom: 16px;
  }
  .time, .tag, .iconBox{
    color :#C1C1C1;
    font-size: 14px;
  }
  .iconList{
    position: absolute;
    width: 200px;
    right: 20px;
    bottom: 6px;
  }
  .iconBox{
    display: inline-block;
    height: 20px;
    line-height: 20px;
    margin-right: 32px;
  }
  .iconBox img{
    width: 16px;
    height: 16px;
    margin-bottom: 5px;
    margin-right: 3px;
  }
  .description{
    padding: 13px 16px;
    border-radius: 10px;
    color: #999999;
    font-size: 16px;
    background: #F6F6F6;
  }
  .part3{
    margin-top: 16px;
    margin-bottom: 56px
  }
  .footer{
    color: #C1C1C1;
    text-align: center;
    padding:32px;
  }
  .left{
    margin-right: 24px;
  }
  .right{
    margin-left: 0;
    height: 357px;
  }
  .headPicture{
    text-align: center;
  }
  .headPicture img{
    height: 80px;
    width: 80px;
    border-radius: 50%;
  }
  .name{
    margin-top: 48px;
    margin-bottom: 16px;
    font-size: 16px;
    text-align: center;
  }
  .slogan{
    text-align: center;
    color: #C1C1C1;
    font-size: 12px;
    padding-bottom: 39px;
    border-bottom: 1px solid #EEEEEE;
  }
  .status{
    text-align: center;
    color: #999999;
    font-weight: bold;
  }
  .statusBox{
    margin: 0;
    margin-right: 20px;
    display: inline-block;
  }
  .statusBox img{
    margin-bottom: 20px;
  }
  .back{
    margin-top: 20px;
    text-align: center;
  }
</style>
