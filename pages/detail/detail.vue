<template>
	<view>
		<swiper class="carousel" autoplay indicator-dots circular>
			<swiper-item v-for="(item,index) in data.imgList" :key="index">
				<view class="image-wrapper">
					<image :src="item.src" :class="item.loaded" mode="aspectFill" @load="imageOnLoad('imgList', index)" ></image>
				</view>
			</swiper-item>
		</swiper>
		<view class="info">
			<view class="title">
				<text :class="{Skeleton:!loaded}">{{data.title}}</text>
				<text :class="{Skeleton:!loaded}">{{data.title2}}</text>
			</view>
			<text class="yticon icon-xia"></text>
		</view>
		<view class="actions">
			<text class="yticon icon-fenxiang2" @click="share"></text>
			<text class="yticon icon-Group-"></text>
			<text class="yticon icon-shoucang" :class="{active: data.favorite}" @click="favorite"></text>
		</view>
		
		<!-- 图文详情 -->
		<view class="detail-desc">
			<view class="d-header">
				<text>图文详情</text>
			</view>
			<rich-text :nodes="desc"></rich-text>
		</view>
		
		<!-- 猜你喜欢 -->
		<view class="guess">
			<view class="d-header">
				<text>猜你喜欢</text>
			</view>
			<view class="guess-list">
				<view v-for="(item, index) in data.guessList" :key="index" class="guess-item" >
					<view class="image-wrapper">
						<image :src="item.src" :class="item.loaded" mode="aspectFill"
						  @load="imageOnLoad('guessList', index)" ></image>
					</view>
					<text class='title clamp' :class="{Skeleton:!loaded}">{{item.title}}</text>
					<text class="clamp" :class="{Skeleton:!loaded}">{{item.title2}}</text>
				</view>
			</view>
		</view>
		<!-- 评论 -->
		<view class="evalution">
			<view class="d-header">
				<text>评论</text>
			</view>
			<view class="eva-list" :class="{Skeleton:!loaded}">
				<view class="eva-item" v-for="(item, index) in data.evaList" :key="index" >
					<image :src="item.src" mode="aspectFill"></image>
					<view class="eva-right">
						<text>{{item.nickname}}</text>
						<text>{{item.time}}</text>
						<view class="zan-box">
							<text>{{item.zan}}</text>
							<text class="yticon icon-shoucang"></text>
						</view>
						<text class="content">{{item.content}}</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 分享 -->
		<share ref="share" :contentHeight="580" :shareList="shareList" ></share>
	</view>
</template>

<script>
	import share from '@/components/share';
	export default {
		components: {
			share
		},
		data() {
			return {
				loaded: false,
				data: {
					guessList: [{},{},{},{}] 
				},
				desc: `
					<div>
						<img style="width:100%;" src="/static/temp/shop.jpg" />
						<img style="width:100%;" src="/static/temp/shop.jpg" />
						<img style="width:100%;" src="/static/temp/shop.jpg" />
						<img style="width:100%;" src="/static/temp/shop.jpg" />
					</div>
				`,
				shareList: []
			};
		},
		async onLoad(){
			let detailData = await this.$api.json('detailData');
			let shareList = await this.$api.json('shareList');
			this.loaded = true;
			this.data = detailData;
			this.shareList = shareList;
			uni.setNavigationBarTitle({
				title: detailData.title
			});
		},
		methods:{
			imageOnLoad(key,index){
				this.$set(this.data[key][index], 'loaded', 'loaded');
			},
			//分享
			share(){
				this.$refs.share.toggleMask();	
			},
			//收藏
			favorite(){
				this.data.favorite = !this.data.favorite;
			}
		},
		//处理遮罩层物理返回键
		onBackPress(){
			if(this.$refs.share.show){
				this.$refs.share.toggleMask();
				return true;
			}
		}
	}
</script>

<style lang="scss">
	page{
		background: $page-color-base;
	}
	.carousel {
		height: 200px;
		.image-wrapper{
			display: flex;
			justify-content: center;
			align-content: center;
			width: 100%;
			height: 100%;
			overflow: hidden;

			image {
				width: 100%;
				height: 100%;
			}
		}
	}
	.scroll-view-wrapper{
		display:flex;
		align-items:center;
		height: 90upx;
		padding: 20upx 0 20upx 40upx;
		background: #fff;
	}
	.info {
		display: flex;
		align-items: center;
		padding: 12upx 40upx;
		background: #fff;

		.title {
			flex: 1;
			display: flex;
			flex-direction: column;
			font-size: $font-lg + 4upx;
			color: $font-color-dark;

			text:last-child {
				font-size: $font-sm;
				color: $font-color-light;
				margin-top: 4upx;
				&.Skeleton{
					width:220upx;
				}
			}
		} 
		.yticon {
			font-size: 44upx;
			color: $font-color-base;
			margin: 0 10upx 0 30upx;
		}
	}

	.actions {
		padding: 12upx 28upx;
		background: #fff;

		.yticon {
			font-size: 46upx;
			color: $font-color-base;
			padding: 10upx 12upx;
			&.active{
				color: #ff4443;
			}
			&:nth-child(2) {
				font-size: 50upx;
			}
		}
	}
	
	/*  详情 */
	.detail-desc{
		background: #fff;
		margin-top: 16upx;
		padding: 20upx 40upx 40upx;
	}
	.d-header{
		display: flex;
		justify-content: center;
		align-items: center;
		height: 80upx;
		margin-bottom: 16upx;
		font-size: $font-base + 2upx;
		color: $font-color-dark;
		position: relative;
			
		text{
			padding: 0 20upx;
			background: #fff;
			position: relative;
			z-index: 1;
		}
		&:after{
			position: absolute;
			left: 50%;
			top: 50%;
			transform: translateX(-50%);
			width: 300upx;
			height: 0;
			content: '';
			border-bottom: 1px solid #ccc; 
		}
	}
	.guess {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		padding: 20upx 40upx 40upx;
		margin-top: 24upx;
		background: #fff;
	}

	.guess-list {
		display: flex;
		flex-wrap: wrap;
		width: 100%;
	}
	.guess-item {
		display: flex;
		flex-direction: column;
		flex: 1;
		overflow: hidden;
		min-width: 40%;
		margin-right: 26upx;
	
		&:nth-child(2n) {
			margin-right: 0;
		}
	
		image {
			width: 100%;
			height: 200upx;
			border-radius: 10upx;
		}
	
		text {
			font-size: $font-sm;
			color: $font-color-light;
			&.Skeleton{
				width: 180upx;
				&.title{
					width: 140upx;
				}
			}
			&.title{
				font-size: $font-base+2upx;
				color: $font-color-dark;
				margin-top:16upx;
				margin-bottom: 8upx;
			}
		}
	}
	.evalution{
		display:flex;
		flex-direction:column;
		background: #fff;
		margin-top: 24upx;
		padding: 40upx 0;
	}

	.eva-item{
		display:flex;
		padding: 20upx 40upx;
		image{
			width: 60upx;
			height: 60upx;
			border-radius: 50px;
			flex-shrink: 0;
			margin-right: 24upx;
		}
	}
	.eva-right{
		display:flex;
		flex-direction:column;
		flex: 1;
		font-size: $font-sm + 2upx;
		color: $font-color-light;
		position:relative;
		.zan-box{
			display:flex;
			align-items:base-line;
			position:absolute;
			top: 10upx;
			right: 10upx;
			.yticon{
				margin-left: 8upx; 
			}
		}
		.content{
			font-size: $font-base;
			color: #333;
			padding-top:20upx;
		}
	}
</style>
