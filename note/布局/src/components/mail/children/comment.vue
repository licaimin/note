<template>
	<div class="container-fluid">
		 <div class="comment-main" v-for="(item ,index ) in comment" :value="item.value" :key="index">
                    <div class="comment-head"><img :src="item.head"></div><!--评论的用户头像-->
                    <div class="comment-content">
                        <div class="commentuser-margin-bottom">
                            <span class="font-span commentuser-margin-right-17">{{item.username}}</span>
                            <span class="font-span commentuser-margin-right">回复了你在{{item.tag}}{{item.articleTitle}}的评论</span>
                            <em  class="font-em">{{item.time}}</em>
                             <span class="font-span answer-style" @click="showAnswerStyle(item,index)">回复</span>
                        </div><!--评论的人名和时间-->
                        
                        <div><p class="font-span margin-bottom-16 ">{{item.content}}</p></div><!--评论的内容-->
                        <div>
                            <div   v-show="item.answer !== [] " v-for="(item2 ,index2 ) in item.answer"  :value="item2.value" :key="index2">
                                <div class="arrow-up"></div><!--回复区域形状实现的三角形-->
                                <div class="comment-answer font-span">
                                    <p class="deleteBtn" slot="reference" ><span type="text "   icon="el-icon-close" class="deleteStyle el-icon-close" @click="deleteAnswer(item,index2)"></span></p>
                                    <!-- <span slot="reference"></span> -->
                                    <p class="margin-bottom-16">{{item.answerUsername}} :
                                    </p>
                                    <p >{{item2}}</p>
                                </div><!--回复的内容-->
                            </div><!--回复的内容和回复背景框-->
                          
                            <div class="flex-between comment-margin-bottom" v-show="commentIndex == index">
                                <textarea  name=text class="comment-textarea" draggable="false" :placeholder="answerCommentUser" v-model="answerCommentValue"></textarea>
                                <button type="button" class="btn btn-primary user-btn btn-my-style" @click="answerCommentFun(item,index)">回复</button>
                            </div><!--回复文本框-->
                        </div><!--回复的内容显示区域-->
                        

                    </div><!--评论回复内容区域-->
                </div><!--评论的用户头像和评论回复区域-->
	</div>
</template>

<script>
export default {
    name: 'noticeMail',
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
            /**回复评论的用户名： 回复XX (placeholder)*/
            answerCommentUser:'',
            /*绑定某个回复框*/
            commentIndex:-1,
            /*回复评论的内容*/
            answerCommentValue:'',
			comment:[{
                        head:'../../static/imgs/works/head.jpg',
                        time:'3天前',
                        username:'JENNIE',
                        tag:'原创文章',
                        articleTitle:"《栅格系统》",
                        content:'想问一下大大，临摹的过程中，临摹一张画可能可以画的很好。但是在临摹下一张的时候，上一张的经验似乎都不在 了，又好像是重新开始，这种怪圈是什么原因造成的。感觉自己临摹的也不少。但是就是心底里很虚。原创不出来。',
                        answerUsername:'JINNANJUN',
                        answer:['从喜欢的漫画起手，在画的过程中发现自己的问题，然后总结问题，抽出时间来针对某个部分进行训练。多临摹，多练习，多画速写。 '],
                        commentCount:12,
                        callCount:12,
                    },
                    {
                        head:'../../static/imgs/works/head.jpg',
                        time:'3天前',
                        username:'JYP',
                        tag:'原创文章',
                        articleTitle:"《栅格系统》",
                        content:'想问一下大大，临摹的过程中，临摹一张画可能可以画的很好。但是在临摹下一张的时候，上一张的经验似乎都不在 了，又好像是重新开始，这种怪圈是什么原因造成的。感觉自己临摹的也不少。但是就是心底里很虚。原创不出来。',
                        answerUsername:'JINNANJUN',
                        answer:[],
                        commentCount:12,
                        callCount:12,
					},
			]
	  }
    },
    methods:{
         /* 点击评论图标，展示回复框*/
        showAnswerStyle(item,index){
            this.commentIndex = this.commentIndex === index ? -1 : index;
            if(this.commentIndex === index){
                 this.answerCommentUser = '回复'+ item.username;
            }
            this.showAnswer = !this.showAnswer;
        },
        /*回复评论*/
        answerCommentFun(item,index){
            if(this.answerCommentValue == ''){
               this.$message({
                    message: '回复内容不能为空',
                    type: 'error'
                });
            }else{
                item.answer.push( this.answerCommentValue );
                item.answerUsername = 'jjj'; //这里插入的是文章的作者名
                this.commentIndex = -1;
                this.answerCommentValue = '';
            }
        },
         //删除回复
        deleteAnswer(item,index2){ 
            item.answer.splice(index2,1)   
            this.$message({
                    message: '删除成功',
                    type: 'success'
                });
        },
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
        border-bottom: 1px solid #EEEEEE;
        padding-bottom: 16px;
    }
    /*全部评论数*/
    .comment-all-margin-bottom{
        margin-bottom: 64px;
    }
    /*评论人的头像*/
    .comment-head img{
    width:48px;
    height:48px;
    border-radius:50%;
    float: left;
}
    /*评论区的内容*/
    .comment-content{
        margin-left: 60px;
    }


    /*评论区的三角形*/
    .arrow-up {
    width:0; 
    height:0; 
    border-left:10px solid transparent;
    border-right:10px solid transparent;
    border-bottom:10px solid rgba(246,246,246,1);
    margin-left: 20px;
    }
    /*回复框*/      
    .comment-answer{
        background-color: rgba(246,246,246,1);
        width: 100%;
        padding: 0 24px;
        padding-bottom: 22px;
        border-radius: 4px;
        margin-bottom: 16px
    }
    /*回复的人*/
    .margin-bottom-16{
        margin-bottom: 16px;
    }
    /*评论的图标*/
    .comment-icon{
        margin: 25px 0;
        display: -webkit-flex; /* Safari */
        display:flex;
        justify-content:flex-end;
    }
    .comment-icon li{
        margin-left: 36px;
    }
	input::-webkit-input-placeholder, textarea::-webkit-input-placeholder {
  color: #C1C1C1;
  font-size: 14px;
}

input:-moz-placeholder, textarea:-moz-placeholder {
  color: #C1C1C1;
  font-size: 14px;
}

input::-moz-placeholder, textarea::-moz-placeholder {
  color: #C1C1C1;
  font-size: 14px;
}

input:-ms-input-placeholder, textarea:-ms-input-placeholder {
  color: #C1C1C1;
  font-size: 14px;
}
/*评论框*/
.comment-textarea{
    background:rgba(246,246,246,1);
    border-radius:4px;
    padding: 13px 14px;
    width: 78%;
    height: 40px;
    border: none;
    resize: none;
    color: #C1C1C1;
    font-size: 14px;
    line-height:14px;
    overflow-y:hidden
}
/*发表按钮的下外边距*/
.comment-margin-bottom{
    margin-bottom: 16px;
}
/*评论数*/
.comment-margin-right{
    margin-right: 16px;
}
/*评论的人与评论时间的间距*/
.commentuser-margin-right{
    margin-right: 24px;
}
.commentuser-margin-right-17{
    margin-right: 17px;
}
.commentuser-margin-bottom{
    margin-bottom: 20px;
}
/*回复按钮--该页面*/
 .answer-style{
     float: right;
     cursor: pointer;
 }
.answer-style:hover{
    color: #928f8f
}
/*关注按钮*/
.user-btn{
    background: #4959F6;
    width: 88px;
    height: 40px;
    border-radius:4px;      
}

/*本页面*/
/*删除评论*/
    .deleteBtn{
        text-align: right;
        padding-top: 5px;
    }
    /*删除按钮的样式*/
    .deleteStyle{
        color: #515151;
        cursor: pointer;
    }
    .deleteStyle:hover{
        color:#aba2a2
    }
</style>