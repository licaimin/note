<template>
	<div class="home">
		<div class="content">
			<div class="nav-bg"> 
				<ul class="nav">
						<li v-for="(item, index) in navSelect" :value="item.value" :key="index"  
						class="nav-item nav-border "  @click="selected(item)"  style="cursor:pointer" 
      					 :class="{active: SelectRouterName == item.path, 'font-px':SelectRouterName !== item.path }" >
							<a class="nav-link"  > {{item.value}}</a>
							<!-- <span>xx</span> -->
						</li>
				</ul>
			</div>	
			<router-view/>
		</div>
	</div>
</template>

<script>
export default {
		name:'mail',
		data(){
			return{
				navSelect:[
					{
						key:'0',
						value:'全部消息',
						path:'allMail',
					},
					{
						key:'1',
						value:'公告',
						path:'noticeMail',
					},
					{
						key:'2',
						value:'评论',
						path:'commentMail'
					}
				],
				activeName:'',
				SelectRouterName:''

				
			}
		},
		methods:{
			selected: function(item) {
			  this.activeName = item.value;
			  this.SelectRouterName = item.path;
					switch ( this.SelectRouterName) {
					case 'allMail': this.$router.push('/mail/all') 
					break
					case 'noticeMail': this.$router.push('/mail/notice') 
					break
					case 'commentMail': this.$router.push('/mail/comment') 
					break
				}
			}
		},
		created(){
			this.SelectRouterName = this.$route.name
			if(this.SelectRouterName == 'mail'){
				// this.activeName = 
				this.$router.push('/mail/all')
				this.SelectRouterName  = 'allMail'
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
	.nav-bg{
		background-color: #FFFFFF;
		margin-top: 24px;
		width: 100%;
		height: 61px;
		margin-bottom: 1px;
	}
	.nav{
		line-height: 41px;
		display: flex;
		flex-direction: row;
	}
	.nav-border{
	}
	.active{
		border-bottom: 4px solid rgba(73,89,246,1);
		font-size: 18px;
		color:rgba(81,81,81,1);
	}
	/* .active:after {
        content: '';
        position: absolute;
        
        top: auto;
      
       
        height: 4px;
        width: 32px;
        background-color: rgba(73,89,246,1);
    } */
	.font-px{
		font-size: 16px;
		color:rgba(153,153,153,1);
	}
</style>