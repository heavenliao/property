<template>
	<div class="mine">
		<div class="mine-top">
			<!-- infos -->
			<div class="mine-top-infos">
				<div class="mine-top-left">
					<h2>{{nickName}}</h2>
					<p>{{signature}}</p>
					<div class="mine-edit" @click="goLinks('edit')">
						<i class="common-icon icon-edit"></i>
						<span>编辑</span>
					</div>
				</div>
				<div class="mine-top-right">
					<img :src="avatarUrl" alt="">
				</div>
			</div>
			<!-- total -->
			<div class="mine-top-total clearfix">
				<ul>
					<li>
						<div>
							<p>{{thisMonthVisitors}}</p>
							<span>本月访客</span>
						</div>
					</li>
					<li>
						<div>
							<p>{{msgNums}}</p>
							<span>消息通知</span>
						</div>
					</li>
					<li v-if="propertyStatus">
						<div>
							<p>{{familyNums}}</p>
							<span>家庭成员</span>
						</div>
					</li>
					<li v-else-if="!propertyStatus">
						<div>
							<p>{{auditNums}}</p>
							<span>审批数量</span>
						</div>
					</li>
				</ul>
			</div>	
		</div>
		<!-- padding -->
		<div class="pd16"></div>
		<!-- bottom -->
		<div class="mine-bottom">
			<div class="mine-item" @click="goLinks('message')">
				<div class="mine-item-left">
					<i class="common-icon icon-notice"></i>
					<span>我的通知</span>
				</div>
				<div class="mine-item-right">
					<i class="common-icon icon-more"></i>
				</div>
			</div>
			<div class="mine-item" @click="goLinks('question')">
				<div class="mine-item-left">
					<i class="common-icon icon-question"></i>
					<span>常见问题</span>
				</div>
				<div class="mine-item-right">
					<i class="common-icon icon-more"></i>
				</div>
			</div>
			<div class="mine-item" @click="goLinks('identify')">
				<div class="mine-item-left">
					<i class="common-icon icon-fill"></i>
					<span>资料完善</span>
				</div>
				<div class="mine-item-right">
					<i class="common-icon icon-more"></i>
				</div>
			</div>
		</div>
		<v-develop v-show="developStatus" @closeDevelop="closeDevelop"></v-develop>
		<nav-bar :page="3"></nav-bar>	
	</div>
</template>

<script>
	import vDevelop from "components/develop/develop";
	import navBar from "components/navBar/navBar";
	export default {
	  name: 'mine',
	  data () {
	    return {
	    	propertyStatus:true,	    	
				auditNums:'',
				avatarUrl:'',
				familyNums:'',
				msgNums:'',
				nickName:'',
				signature:'',
				thisMonthVisitors:'',
				developStatus:false,
	    }
	  },
	  components:{
	    navBar,vDevelop
	  },
	  mounted(){
	  	let role = localStorage.getItem("role")
	  	if(role == 'property'){
	  		this.propertyStatus = false
	  	}else if(role == 'owner'){
	  		this.propertyStatus = true
	  	}
	  	this.getUserInfo();
	  },
	  methods:{
	  	closeDevelop(val){
	  		this.developStatus = val;
	  	},
	  	getUserInfo(){
	  		this.$http({
	        method: "post",
	        url: "/wechat/officialAccount/user/userInfo",
	        data: this.$qs.stringify({
	        	// 'authToken':localStorage.getItem('authToken')
	        })
	      }).then((res) => {
	      	let result = res.data.result;
	      	this.auditNums = result.auditNums || 0
					this.avatarUrl = result.avatarUrl
					this.familyNums = result.familyNums || 0
					this.msgNums = result.msgNums || 0
					this.nickName = result.nickName
					this.signature = result.signature
					this.thisMonthVisitors = result.thisMonthVisitors || 0
	    	}).catch((err) => {
	      });	
	  	},
	  	goLinks(url){
	  		if(url == 'identify'){
	  			this.$http({
		        method: "post",
		        url: "/wechat/mini/user/identityInfo",
		        data: this.$qs.stringify({
		        	authToken:localStorage.getItem('authToken')
		        })
		      }).then((res) => {
		      	let result = res.data.result;
		      	this.$router.push({path:''+url,query:{from:'mine',headPortraitUrl:result.headPortraitUrl,identityImgUrl:result.identityImgUrl,idCardNo:result.idCardNo}})
		    	}).catch((err) => {
		      });
	  		}else if(url == 'edit'){
	  			this.developStatus = true;
	  		}else{
	  			this.$router.push({path:''+url})
	  		}	  		
	  	},
	  }
	}
</script>

<style lang="stylus" type="text/stylus" scoped>
	.mine
		padding-top 1rem
		.mine-top
			width 6.74rem
			margin 0 .38rem
			.mine-top-infos
				width 100%
				display flex
				justify-content space-between
				.mine-top-left
					h2
						font-size .4rem
						color #4A4A4A
						line-height .56rem
						padding-bottom .1rem
					p
						font-size .26rem
						color #9B9B9B
						line-height .37rem
						padding-bottom .22rem
					.mine-edit
						display flex
						align-items center
						width 1.2rem
						height .52rem
						line-height .52rem
						background rgba(241,242,243,1)
						border-radius .14rem
						i
							margin-left .17rem
							margin-bottom .04rem
						span
							margin-left .08rem
							font-size .26rem
							color #4A4A4A					
				.mine-top-right
					margin-top .1rem
					img
						width 1.4rem
						height 1.42rem
						border-radius 50%
						overflow hidden
			.mine-top-total
				margin-top .8rem
				width 100%
				padding-bottom .48rem
				ul
					width 100%
					display flex
					align-items center
					justify-content space-around
					li
						width 33.3%
						height .87rem
						text-align center
						border-right 1px solid #F7EFEF
						&:last-child
							border-right none
						p
							font-size .5rem
							color #4A4A4A
							line-height 1
							padding-bottom .12rem
							font-weight 700
						span
							font-size .26rem
							color #9B9B9B
		.mine-bottom
			width 6.74rem
			margin 0 .38rem
			.mine-item
				height 1.32rem
				border-bottom 1px solid #F7EFEF
				display flex
				align-items center
				justify-content space-between
				.mine-item-left
					
					display flex
					align-items center
					i
						margin-right .08rem
					span
						font-size .3rem
						color #4A4A4A
				.mine-item-right
					i
						margin-right .26rem
			
</style>