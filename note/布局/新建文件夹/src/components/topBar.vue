<template>
	<div class="topBar">
		<div class="top">
	        <span class="top_logo">logo</span>
		    <div class="box1">
		    	<span :class="$route.path.endsWith('/')?linkClass[0]:linkClass[1]" @click="$router.replace('/')">首页</span>
		    	<span :class="$route.path.includes('/photo')?linkClass[0]:linkClass[1]" @click="$router.replace('/photo')">摄影</span>
		    	<span :class="$route.path.includes('/articles')?linkClass[0]:linkClass[1]" @click="$router.replace('/articles')">文章</span>
		    </div>
		    <!-- 搜索框 -->
			<div class="inputCtn">
				<input type="text" name="search" class="searchIpt" placeholder="输入关键字" @keyup='keyupFn(searchVal)' v-model='searchVal'/>
				<img class='search' src="../../static/imgs/search.png" @click='searchFn(searchVal)'>
			</div>
			<div class="hoverBox"  :style="$store.state.isLogin?'right:80px;':'right:160px;'">
				<span class="hover_b" @click="$router.replace('/mail')" v-if='$store.state.isLogin' @mouseover="hoverFn(1)" @mouseout="hoverFn(2)">
				站内信
					<i class="noteIcon" v-if="hasNotice"></i>
				</span>
				<!-- 站内信滑过框 -->
				<div class="infoBox" v-if="isInfoShow" @mouseover="hoverFn(1)" @mouseout="hoverFn(2)">
					<div v-for="item of notice" class="noticeBox">
						<div class="noticeTitle ellipsis">{{item.title}}</div>
						<span class="noticeDate">{{item.date}}</span>
					</div>
					<div class="view_more" @click="$router.replace('/mail')">查看更多</div>
				</div>
			<!-- </div>
			<div class="hoverBox" :style="$store.state.isLogin?'':'margin-left:15%;'"> -->
				<span class="hover_b" @click="toContribute(3)" @mouseover="hoverFn(3)" @mouseout="hoverFn(4)">投稿</span>
				<!-- 投稿划过框 -->
				<div class="itemsBox" v-if="isItemShow" @mouseover="hoverFn(3)" @mouseout="hoverFn(4)" :style="$store.state.isLogin?'':'left:-19px;'">
					<span v-for="(item,index) of items" :key='item' class="item_" @click="toContribute(index)">{{item}}</span>
				</div>
			</div>
			<div class="btnCtn">
				<el-button  type="primary" class="btn btn1" v-if='!$store.state.isLogin' @click="loginClk('tab2')">注册</el-button>
				<el-button  type="primary" class="btn btn2" v-if='!$store.state.isLogin' @click="loginClk('tab1')">登陆</el-button>
			</div>
			<img :src="avatar" class="avatar1" @click="$router.replace('/personalHome')" v-if='$store.state.isLogin' @mouseover='hoverFn(5)' @mouseout='hoverFn(6)'>
			<!-- 个人头像滑过框 -->
			<div class="selfBox" v-if='isSelfShow' @mouseover='hoverFn(5)' @mouseout='hoverFn(6)'>
				<img :src="avatar" class="avatarBig">
				<div class="nickName ellipsis">{{nickName}}</div>
				<div class="sign ellipsis">个人签名：{{sign}}</div>
				<div class="btmBox" @click="$router.replace('/personalHome')">
					<img src="../../static/imgs/self.png">
					<span>个人中心</span>
				</div>
				<div class="btmBox" @click="exitLogin">
					<img src="../../static/imgs/out.png">
					<span>退出登录</span>
				</div>
			</div>
    	</div>

    	<div class="greyCtn" v-if='isLgShow' id="greyCtn">
    		<login-bar v-on:listenCancel='recieveCancel' v-bind:tab='tab'></login-bar>
    	</div>
	</div>
</template>
<script>
	import loginBar from './loginBar'
	import Vue from 'vue'
	export default{
		name:'topBar',
		components:{
			loginBar
		},
		data(){
			return{
				linkClass:['active','link'],
				isLgShow:false,
				isSelfShow:false,
				nickName:'',
				sign:'',
				hasNotice:true,//是否有站内信
				items:['艺术作品','摄影','文章'],
				isInfoShow:false,
				isItemShow:false,
				notice:[{title:'你的会员已到期，请赶快充值',date:'2018-12-30'},{title:'快来抽奖啦，下一个锦鲤就是你',date:'2019-01-28'},{title:'你的会员已到期，请赶快充值',date:'2018-01-29'}],
				searchVal:'',
				tab:'tab1',
				avatar:''
			}
		},
		methods:{
			//注册,登录按钮
			loginClk(tab){
				var that=this
				this.tab=tab
				this.isLgShow=true
				this.bodyHeight=window.innerHeight+"px";
				this.bodyWidth=window.innerWidth
				console.log('h',that.bodyHeight)
				var t1=setTimeout(()=>{
					let el=document.getElementById('greyCtn');
					el.style.height=that.bodyHeight;
					el.style.width=that.bodyWidth+'px';
				},10)
				$('body').css('overflow','hidden');
			},
			recieveCancel(data){
				this.isLgShow=data
			},
			hoverFn(idx){
      			switch(idx){
      				case 1:{
      					this.isInfoShow=true;
      				}break;
      				case 2:{this.isInfoShow=false}break;
      				case 3:{this.isItemShow=true}break;
      				case 4:{this.isItemShow=false}break;
      				case 5:{
      					//个人头像滑过
      					var userData=JSON.parse(this.$store.state.userData)
      					this.isSelfShow=true
      					this.nickName=userData.nickname
						this.sign=userData.personalDescription
						
      				}break;
      				case 6:{this.isSelfShow=false}break;
      			}
      		},
      		//退出登录
      		exitLogin(){
      			this.$store.dispatch('exit')
      			this.isSelfShow=false
      			this.$router.replace('/')
      		},	
      		//搜索
      		searchFn(val){
				this.$router.replace('/search')	
				this.$store.dispatch('searchVal',val)
				this.searchReq(val)	
      		},
      		//监听回车键
      		keyupFn(val){
      			if (event.keyCode == "13") {
					this.$router.replace('/search')
					this.$store.dispatch('searchVal',val)
					this.searchReq(val)	
            	}
      		},
      		//获得搜索结果
      		searchReq(val){
      			console.log(val)
      			var that=this
      			var sort=that.$store.state.sortNum,
      				type=that.$store.state.typeNum 
        		this.$axios({
        			method:'get',
        			url:'/api/work/search?key='+val+'&pageNum=1&pageSize=20&sort='+sort+'&type='+type
        		}).then((res)=>{
        			console.log(res)
        			if(res.data.code==20000){
        				this.$store.dispatch('total',res.data.data.total)
				        this.$store.dispatch('search',res.data.data.rows)
        			}else{
        				this.$message.error('搜索失败');
        			}
        		}).catch((err)=>{
        			console.log(err)
        			this.$message.error('网络错误');
        		})
      		},
      		//投稿选项
      		toContribute(idx){
      			if(this.$store.state.isLogin==true){
      				switch(idx){
	      				case 0:{
	      					this.$router.replace('/newWork')
	      				}break;//艺术作品
	      				case 1:{
	      					this.$router.replace('/newPhotograph')
	      				}break;//摄影
	      				case 2:{
	      					this.$router.replace('/newArticle')
	      				}break;//文章
	      				default:{
	      					this.$router.replace('/newWork')
	      				}break;
      				}
      			}else{
      				this.loginClk('tab1')
      			}
      		}
		},
		mounted(){
			if(this.$store.state.userData){
				var userData=JSON.parse(this.$store.state.userData)
				this.avatar=userData.avatar
				this.nickName=userData.nickname
				this.sign=userData.personalDescription
			}
		}
	}
	new Vue({
		events:{
			'show':function(msg) {
				this.isLgShow=msg;
			}
		}
	})
</script>
<style scoped>
	*{
		font-size:14px;
		font-family:PingFangSC-Medium;
		font-weight:500;
		color:rgba(81,81,81,1);
		line-height:14px;
	}
	.topBar{
		width: 100%;
		height:56px;
		box-shadow:0px 0px 8px 0px rgba(0,0,0,0.12);
		z-index: 99;
		position: fixed;
		top:0;
		left:0;
		background-color: white;
	}
	.top{
		box-sizing: content-box;
		height: 56px;
		width: 1136px;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: space-between;
		margin: 0 auto;
		position: relative;
	}
	.top_logo{
		width: 50px;
		height: 16px;
		color: #5867F7;
		font-size: 15px;
	}
	.nav{
		display: flex;
		flex-direction: row;
	}
	.box1{
		width: 180px;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		margin-left: 212px;
		position: absolute;
	}
	.link{
		color: #515151;
	}
	.link:hover{
		color:#4959F6;
		cursor: pointer;
	}
	.active{
		color:#4959F6;
		cursor: pointer;
	}
	.inputCtn{
		width: 256px;
		position: absolute;
		margin-left: 440px;
	}
	.searchIpt{
		width: 100%;
		height: 32px;
		border:none;
		border-radius: 16px;
		background-color: #F6F6F6;
		padding-left: 10px;
	}
	input::-webkit-input-placeholder {
		font-size: 12px;
		color:rgba(193,193,193,1);
     }
	.search{
		width: 16px;
		height: 16px;
		position: absolute;
		top:8px;
		left:90%;
	}
	.hoverBox{
		height: 100%;
		position: absolute;
		display: flex;
	}
	.hover_b{
		height: 100%;
		width: 82px;
		display: flex;
		align-items: center;
		justify-content: center;
		position: relative;
	}
	.hover_b:hover{
		height: 100%;
		width: 82px;
		background-color: #F1F2FF;
		cursor: pointer;
	}
	.avatar1{
		width: 60px;
		height: 60px;
		border-radius: 50%;
		/*background-color: black;*/
		border:10px solid rgba(0,0,0,0);
	}
	.btn{
		color:white;
		background-color: #4959F6;
		height: 32px;
		line-height: 0;
		text-align: center;
		padding: 18px;
		border:none;
	}
	.btn1{
		border:none;
		background-color: #F6F6F6;
		color: #4959F6
	}
	.btnCtn{
		display: flex;
		margin-left: 2%;
	}
	.noteIcon{
		width:6px;
		height:6px;
		background:rgba(73,89,246,1);
		position: absolute;
		right: 5px;
	    top: 15px;
	    border-radius: 50%;
	}

	/*弹框灰背景*/
	.greyCtn{
		background-color: rgba(127, 127, 127, 0.3);
		z-index: 999;
		position: absolute;
		top: 0;
		left: 0;
		width:100%;
	}

	/*个人头像框*/
	.selfBox{
		width: 280px;
		height: 236px;
		position: absolute;
		top: 56px;
		right: 0;
		background-color: white;
		background:rgba(255,255,255,1);
		box-shadow:0px 0px 8px 0px rgba(0,0,0,0.12);
		border-radius:4px;
	}
	.avatarBig{
		width: 56px;
		height: 56px;
		border-radius: 50%;
		margin:16px auto;
		display: block;
	}
	.nickName{
		display: block;
		margin: 2px auto;
		width: 114px;
		font-size: 14px;
	}
	.sign{
		display: block;
		margin: 11px auto;
		width: 216px;
		font-size: 12px;
		color:#DBDBDB;
	}
	.btmBox{
		padding: 15px;		
		font-size: 14px;
		line-height: 14px;
		cursor: pointer;
	}
	.btmBox:hover{
		background-color: #F7F7F7;
	}
	.btmBox img{
		width: 16px;
		height: 16px;
	}

	/*站内信滑过框*/
	.infoBox{
		width:280px;
		background-color: white;
		box-shadow:0px 0px 8px 0px rgba(0,0,0,0.12);
		border-radius:4px;
		padding-top: 1px;
		position: absolute;
		left: -100px;
		top: 56px;
	}
	.noticeBox{
		display: inline-block;
		margin-top:24px;
		padding-left: 24px;
		text-align: left;
		display: flex;
		flex-direction: row;
	}
	.noticeTitle{
		width: 140px;
		font-size:14px;
		font-family:PingFangSC-Medium;
		font-weight:500;
		color:rgba(81,81,81,1);
		line-height:14px;
		margin-right: 12px;
	}
	.noticeDate{
		font-size:12px;
		font-family:PingFangSC-Regular;
		font-weight:400;
		color:rgba(193,193,193,1);
		line-height:12px;
	}
	.view_more{
		padding-top: 16px;
		padding-bottom: 16px;
		margin: 0 auto;
		margin-top: 24px;
		background:rgba(247,247,247,1);
		border-radius:0px 0px 4px 4px;
		font-size:14px;
		font-family:PingFangSC-Medium;
		font-weight:500;
		color:rgba(153,153,153,1);
		line-height:14px;
		cursor: pointer;
	}
	/*投稿划过框*/
	.itemsBox{
		width: 120px;
		padding:16px 0 16px 0;
		background-color: white;
		background:rgba(255,255,255,1);
		box-shadow:0px 0px 8px 0px rgba(0,0,0,0.12);
		border-radius:4px;
		position: absolute;
		left: 60px;
		top: 56px;
	}
	.item_{
		padding: 8px;
		padding-left: 24px;
		font-size: 14px;
		display: block;
		text-align: left;
		cursor: pointer;
	}
	.item_:hover{
		background:rgba(73,89,246,0.08);
		color:#4959F6;
	}
</style>