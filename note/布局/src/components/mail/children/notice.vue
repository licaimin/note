<template>
	<div class="container-fluid">
		 <div class="comment-main" v-for="(item ,index ) in comment" :value="item.value" :key="index">
                    <div class="comment-head">
                        <span v-show="!item.head" class="notice-icon" >  
                            <span class="notice-icon-font" >公</span>  
                        </span>
                        <img v-show="item.head" :src="item.head">
                    </div><!--公告-->
                    <div class="comment-content">
                        <div class="commentuser-margin-bottom"><span class="font-span commentuser-margin-right">{{item.username}}</span><em class="font-em">{{item.time}}</em></div>
                        <!--评论的人名和时间-->
                        <div><p class="font-span margin-bottom-31">{{item.content}}</p></div><!--评论的内容-->

                    </div><!--评论回复内容区域-->
                </div><!--评论的用户头像和评论回复区域-->
           
	</div>
</template>

<script>
export default {
    name: 'commentMail',
    data () {
	  return {
		   imgs:{
              //头像
              head:'../../static/imgs/works/head.jpg',
              //点亮收藏量
              lcollection:'../../static/imgs/works/lcollection.png',
              dcall:'../../static/imgs/works/dcall.png',
              //作品标题的浏览量，收藏量，点赞量
              view:'../../static/imgs/works/view.png',
              call:'../../static/imgs/works/call.png',
              collection:'../../static/imgs/works/collection.png',
			},
			comment:[{
                        time:'3天前',
                        username:'公告',
                        content:'想问一下大大，临摹的过程中，临摹一张画可能可以画的很好。但是在临摹下一张的时候，上一张的经验似乎都不在 了，又好像是重新开始，这种怪圈是什么原因造成的。感觉自己临摹的也不少。但是就是心底里很虚。原创不出来。',
                    },
                    {
                        time:'3天前',
                        username:'公告',
                        content:'想问一下大大，临摹的过程中，临摹一张画可能可以画的很好。但是在临摹下一张的时候，上一张的经验似乎都不在 了，又好像是重新开始，这种怪圈是什么原因造成的。感觉自己临摹的也不少。但是就是心底里很虚。原创不出来。',
                    },
                    {
                        head:'../../static/imgs/works/head.jpg',
                        time:'3天前',
                        username:'abi',
                        content:'关注了你',

                    }
			]
	  }
	}
}
	
</script>

<style scoped>
	.container-fluid{
		 background-color: #FFFFFF;
		 padding: 0 32px;
		 text-align: left;
         margin-bottom: 110px;

	}



	/*文字大小*/
	/*字体大小*/
    .font-span{
        font-size: 14px;
        color: #515151;
        line-height:18px;
        font-weight:500;
    }
    .font-em{
         font-size: 14px;
        color: #C1C1C1;
        line-height:14px;
        font-weight:500;
    }
    .font-large-em{
        font-size: 16px;
        color: #999999;
        line-height:16px;
        font-family:PingFangSC-Medium;
        font-weight:500;
    }

	/*左右布局*/
	/*左右布局*/
	.flex-between{
		display: -webkit-flex; /* Safari */
		display:flex;
		justify-content:space-between
	}
	 /*评论区域*/
    .comment-main{
        margin-top: 24px;
        border-bottom: 1px solid #EEEEEE
    }
    /*全部评论数*/
    .comment-all-margin-bottom{
        margin-bottom: 64px;
    }
    
    /*评论区的内容*/
    .comment-content{
        margin-left: 60px;
    }
    /*评论的人与评论时间的间距*/
    .commentuser-margin-right{
        margin-right: 24px;
    }
    .commentuser-margin-bottom{
        margin-bottom: 20px;
    }
    .margin-bottom-31{
        margin-bottom: 31px;
    }
 /*评论人的头像*/
    .comment-head img{
    width:48px;
    height:48px;
    border-radius:50%;
    float: left;
}
/*该页面另写的*/
/*公告的图标*/
.notice-icon{
    border-radius: 50%;    
    width:48px;
    height:48px;
    background:rgba(73,89,246,1);   
    display: inline-block;         
    vertical-align: top;
}
.notice-icon-font{
   display: block;         
   text-align: center;
    font-size:24px;
    font-family:PingFangSC-Semibold;
    font-weight:600;
    color:rgba(255,255,255,1);
    line-height:48px;
}
.comment-head{
    float: left;
}
</style>