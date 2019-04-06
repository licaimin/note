<template>
  <div class="home">
    <div class="content">
      <div class="information">
        <div class="userPicture">
          <img :src="material.avatar" alt="用户头像">
        </div>
        <span>{{material.nickname}}</span>
        <p>{{material.personalDescription}}</p>
      </div>
      <b-row class="atten">
        <b-col cols="2" offset="3">
          <span>{{material.popularity}}</span>
          <p>人气</p>
        </b-col>
        <b-col cols="2">
          <router-link :to="{path:'/personalHome/focusfans',name:'focusfans',params:{tabs: 'fans'}}" class="personalTag">
            <span>{{material.fansCount}}</span>
            <p>粉丝</p>
          </router-link>
        </b-col>
        <b-col cols="2">
          <router-link :to="{path:'/personalHome/focusfans',name:'focusfans',params:{tabs: 'focus'}}" class="personalTag">
            <span>{{material.followCount}}</span>
            <p>关注</p>
          </router-link>
        </b-col>
      </b-row>
      <div class="tabBox">
        <ul class="nav nav-pills mb-2 tab" id="pills-tab" role="tablist">

          <li class="nav-item">
            <a class="nav-link active" id="pills-favorite-tab" data-toggle="pill" href="#pills-favorite" role="tab" aria-controls="pills-favorite" aria-selected="false">
              关注
              <span></span>
            </a>
          </li>
          <li class="nav-item" style="margin-left: 88px;">
            <a class="nav-link" id="pills-record-tab" data-toggle="pill" href="#pills-record" role="tab" aria-controls="pills-record" aria-selected="true">
              粉丝
              <span></span>
            </a>
          </li>
        </ul>
        <div class="tab-content" id="pills-tabContent" style="background: #f6f6f6;position: relative;top: -0.5rem;">
          <!--关注-->
          <div class="tab-pane fade show active" style="padding-top: 32px;padding-bottom: 10px;" id="pills-favorite" role="tabpanel" aria-labelledby="pills-favorite-tab">
            <b-card-group deck class="focusList">
              <div v-for="item in focusList">
                <b-card style="max-width: 20rem;"
                        class="mb-2 focus">
                  <b-img :src="item.avatar" @click="checkPeople(item.id)"
                         style="cursor: pointer"
                         rounded="circle" width="64" height="64"
                         alt="img" class="m-1 focusHeadPicture" />
                  <p class="focusName" style="cursor: pointer" @click="checkPeople(item.id)">{{item.focusName}}</p>
                  <p class="focusSlogan">{{item.personalDescription}}</p>
                  <b-button variant="primary" class="followTrue"
                            v-if="!item.islike" @click="followThem(true, item)">
                    <span>关注</span>
                  </b-button>
                  <b-button variant="primary" class="followFalse"
                            v-if="item.islike" @click="followThem(false, item)">
                    <span>已关注</span>
                  </b-button>
                </b-card>
              </div>
            </b-card-group>
            <b-pagination v-if="focusList != undefined && focusList.length!=0"
                              :total-rows="focusTotal" :per-page="20"
                              v-model="focusPageNum" @click="getFans"
                              align="center" hide-goto-end-buttons  />
          </div>

          <!--粉丝-->
          <div class="tab-pane fade" style="padding-top: 32px;padding-bottom: 10px;" id="pills-record" role="tabpanel" aria-labelledby="pills-record-tab">
            <b-card-group deck class="fansList">
              <div v-for="item in fansList">
                <b-card style="max-width: 20rem;"
                        class="mb-2 fans">
                  <b-img :src="item.avatar" @click="checkPeople(item.id)"
                         style="cursor: pointer"
                         rounded="circle" width="64" height="64"
                         alt="img" class="m-1 fansHeadPicture" />
                  <p class="nickname" style="cursor: pointer" @click="checkPeople(item.id)">{{item.nickname}}</p>
                  <p class="fansSlogan">{{item.personalDescription}}</p>
                  <b-button variant="primary" class="followTrue"
                            v-if="!item.islike" @click="followThem(true, item)">
                    <span>关注</span>
                  </b-button>
                  <b-button variant="primary" class="followFalse"
                            v-if="item.islike" @click="followThem(false, item)">
                    <span>已关注</span>
                  </b-button>
                </b-card>
              </div>
            </b-card-group>
            <b-pagination :total-rows="fansTotal" :per-page="20"
                          v-model="fansPageNum" @click="getFollow"
                          v-if="fansList != undefined && fansList.length!=0"
                          align="center" hide-goto-end-buttons  />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "focusfans",
    data: function () {
      return {
        material:{
          // avatar: "https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg",
          // nickname:"山河湖海天地",
          // personalDescription:"这个人很懒，什么都没有写…撒大撒",
          // popularity: 5100,
          // fansCount: 36,
          // followCount: 52,
        },

        currentPage: 1,
        fansTotal: 20,
        fansPageNum:1,
        fansList:[{
        //   avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
        //   nickname: '笑嘻嘻',
        //   personalDescription:'五分钟前',
        //   islike: true
        // },{
        //   avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
        //   nickname: '哭唧唧',
        //   personalDescription:'五分钟前',
        //   islike: true
        }],
        focusTotal: 20,
        focusPageNum:1,
        focusList:[{
        //   avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
        //   focusName: '笑嘻嘻',
        //   personalDescription:'五分钟前',
        //   islike: true
        // },{
        //   avatar: 'https://www.qq745.com/uploads/allimg/160722/8-160H2103338.jpg',
        //   focusName: '哭唧唧',
        //   personalDescription:'五分钟前',
        //   islike: true
        }]
      }
    },
    methods:{
      // 访问他人资料
      checkPeople:function(id){
        this.$router.push({path: '/personalHome', query:{id: id}})
      },
      // 个人信息初始化
      getMaterial:function(){
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/profile',
          url: '/' + that.material.id
        }).then((res)=>{
          that.material = res.data.data;
        }).catch((err)=>{
          console.log(err);
        })
      },
      // 粉丝初始化
      getFans:function(){
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/profile/fans',
          params:{
            pageNum: that.fansPageNum,
            pageSize: 20,
            userId: that.material.id,
          }
        }).then((res)=>{
          that.fansTotal = res.data.data.total;
          that.fansList = res.data.data.rows;
        }).catch((err)=>{
          console.log(err);
        })
      },
      // 关注初始化
      getFollow:function(){
        let that = this;
        this.$axios({
          method: 'get',
          baseURL: '/api/profile/follows',
          params:{
            pageNum: that.focusPageNum,
            pageSize: 20,
            userId: that.material.id,
          }
        }).then((res)=>{
          that.focusTotal = res.data.data.total;
          that.focusList = res.data.data.rows;
        }).catch((err)=>{
          console.log(err);
        })
      },
      follow:function(id){
        let that = this;
        this.$axios({
          method: 'post',
          baseURL: '/api/follow',
          url: '/'+ id
        }).then((res)=>{
          console.log(res);
        }).catch((err)=>{
          console.log(err);
        })
      },
      cancelFollow:function(id){
        let that = this;
        this.$axios({
          method: 'del',
          baseURL: '/api/follow',
          url: '/'+ id
        }).then((res)=>{
          console.log(res);
        }).catch((err)=>{
          console.log(err);
        })
      },
      followThem: function (state, item) {
        if (state === true){
          this.follow(item.id);
          item.islike = !item.islike;
        }else if (state === false){
          this.cancelFollow(item.id);
          item.islike = !item.islike;
        }
      }
    },
    created:function () {
      let that = this;
      var userData=JSON.parse(this.$store.state.userData)
      console.log(this.$route.params.tabs);
      this.getFollow();
      this.getFans();
      this.material.id=userData.id
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
  .personalTag{
    text-decoration: none;
  }
  .tabBox{
    margin-top: 48px;
  }
  .tabBox .tab{
    text-align: center;
    margin: 0 auto;
    margin-bottom: 0;
    width: 220px;
  }
  .tab-pane{
    width: 80%;
    margin: 0 auto;
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
  .category .active{
    color: #fff;
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
  .author img{
    width: 24px;
    height: 24px;
    border-radius: 50%;
  }
  .author strong{
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
  .fansList, .focusList{
    text-align: center;
  }
  .fans, .focus{
    width: 208px;
    height: 208px;
  }
  .fans .fansName, .focus .focusName{
    margin-top: 12px;
    margin-bottom: 8px;
    font-size: 16px;
    line-height: 16px;
    color: #515151;
    font-weight: bold;
  }
  .fans .fansSlogan, .focus .focusSlogan{
    font-size: 14px;
    color: #999999;
    line-height: 14px;
  }
  .followTrue, .followFalse{
    padding: 0;
    border: none;
    height: 32px;
    width: 64px;
    font-size: 14px;
    line-height: 32px;
  }
  .followFalse{
    background-color: #F6F6F6;
    color: #C1C1C1;
  }
</style>
