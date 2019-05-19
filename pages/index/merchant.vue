<template>
	<view class="container">
		<!-- 小程序头部兼容 -->
		<!-- #ifdef MP -->
		<view class="mp-search-box">
			<input class="ser-input" type="text" value="输入关键字搜索" />
		</view>
		<!-- #endif -->
		<!-- 头部轮播 -->
		<view class="carousel-section">
			<!-- 背景色区域 -->
			<view class="titleNview-background" :style="{backgroundColor:titleNViewBackground}"></view>
			<!-- 标题栏和状态栏占位符 -->
			<view class="titleNview-placing"></view>
			<swiper class="carousel" indicator-dots autoplay circular @change="swiperChange">
				<swiper-item v-for="(item, index) in carouselList" :key="index" class="carousel-item" @click="navToDetailPage({title: '轮播广告'})">
					<image :src="item.src" />
				</swiper-item>
			</swiper>
		</view>
		<!--二级菜单-->
		<view class="cate-section">
			<view class="cate-item" v-for="(navItem, index) in navList" :key="index" @click="navigateTo(navItem.link)">
				<image :src="navItem.image"></image>
				<text v-text="navItem.text"></text>
			</view>
		</view>
		<!-- new -->
		<view class="new">
			<view class="n-header">
				<view class="n-left">同城头条</view>
			</view>
			<!--轮播new
			<swiper class="carousel" indicator-dots autoplay circular @change="swiperChange">
				<swiper-item v-for="(item, index) in goodsList" :key="index" class="n-right" @click="navToNew(item)">
					<text class="new-title">{{item.title}}</text>
				</swiper-item>
			</swiper>
			* -->
		</view>
		<view class="buttons-main">
			<view v-for="(item,index) in tab" :key="index" @click="select(index)" :class="timeIndex===index ? 'default default-active':'default'">
				{{item.title}}
			</view>
		</view>
		<!--滑动菜单-->
		<view class="hd-tab">
			<view class="hd-list" v-for="(item,index) in merchantList" :key="index" @click="merchantSelect">
				<view class="image-wrapper">
					<image :src="item.image" mode="aspectFill"></image>
				</view>
				<text class="name">店铺名称</text>
				<text class="time">营业时间</text>
				<text class="kind">分类</text>
				<text class="distance">距离</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				titleNViewBackground: '',
				swiperCurrent: 0,
				swiperLength: 0,
				timeIndex: 0,
				carouselList: [],
				newList: [],
				merchantList:[],
				navList: [{
						text: '批发供货',
						image: '/static/icons/bianmin.jpg',
						link: ''
					},
					{
						text: '餐饮美食',
						image: '/static/icons/cm.png',
						link: ''
					},
					{
						text: '休闲娱乐',
						image: '/static/icons/xy.png',
						link: ''
					},
					{
						text: '美容美发',
						image: '/static/icons/mr.png',
						link: ''
					},
					{
						text: '购物商场',
						image: '/static/icons/pintuan.jpg',
						link: ''
					},
					{
						text: '教育培训',
						image: '/static/icons/book.png',
						link: ''
					},
					{
						text: '服装鞋帽',
						image: '/static/icons/xie.png',
						link: ''
					},
					{
						text: '近郊旅游',
						image: '/static/icons/ly.png',
						link: ''
					},
					{
						text: '装饰建材',
						image: '/static/icons/ju.png',
						link: ''
					},
					{
						text: '汽车服务',
						image: '/static/icons/car.png',
						link: ''
					}
				],
				tab: [{
						title: '附近'
					},
					{
						title: '推荐'
					},
					{
						title: '人气'
					},
					{
						title: '最热'
					}
				],
				goodsList: []
			};
		},
		onLoad() {
			this.loadData();
		},
		methods: {
			async loadData() {
				let carouselList = await this.$api.json('carouselList');
				this.titleNViewBackground = carouselList[0].background;
				this.swiperLength = carouselList.length;
				this.carouselList = carouselList;
				let goodsList = await this.$api.json('goodsList');
				this.goodsList = goodsList || [];
				this.newList = goodsList;
				this.merchantList=goodsList;
			},
			//轮播图切换修改背景色
			swiperChange(e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				this.titleNViewBackground = this.carouselList[index].background;
			},
			navigateTo(nav) {
				console.log(nav);
				uni.navigateTo({
					url: nav
				})
			},
			//详情页
			navToDetailPage(item) {
				//测试数据没有写id，用title代替
				let id = item.title;
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
			select(index) {
				this.timeIndex = index;
				this.$set(this.merchantList,)
			},
		}
	}
</script>

<style lang="scss">
	/* 头部 轮播图 */
	.carousel-section {
		position: relative;
		padding-top: 10px;

		.titleNview-placing {
			height: var(--status-bar-height);
			padding-top: 44px;
			box-sizing: content-box;
		}

		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
	}

	.carousel {
		width: 100%;
		height: 350upx;

		.carousel-item {
			width: 100%;
			height: 100%;
			padding: 0 28upx;
			overflow: hidden;
		}

		image {
			width: 100%;
			height: 100%;
			border-radius: 10upx;
		}
	}

	.cate-section {
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-wrap: wrap;
		margin-top: 28upx;
		padding: 30upx;
		background: #fff;

		.cate-item {
			min-width: 20%;
			margin: 14upx 0;
			display: flex;
			flex-direction: column;
			align-items: center;
			font-size: $font-sm + 2upx;
			color: $font-color-dark;
		}

		image {
			width: 88upx;
			height: 88upx;
			margin-bottom: 16upx;
			border-radius: 100%;
			opacity: .8;
			box-shadow: 0 0 20upx rgba(255, 120, 120, 0.3);
		}
	}

	/* new */
	.new {
		background: #fff;
		margin-top: 10upx;

		.n-header {
			margin-top: 10upx;
			display: flex;
			align-items: center;
			height: 100upx;
			line-height: 1;
			background: #fff;

			.n-left {
				position: absolute;
				color: #FE433F;
				font-weight: 700;
				font-size: 16px;
				left: 15px;
			}

			//new轮播图
			/*.right-item {
				width: 100%;
				line-height: 40px;
				position: absolute;
				float: left;

				.item-title {
					width: 100%;
					color: #2f2f2f
				}
			}*/
		}
	}

	.buttons-main {
		display: flex;
		justify-content: space-around;
		align-items: center;
		flex-wrap: wrap;
		margin-top: 10upx;
		background: #fff;
		//height: 10px;

		.default {
			min-width: 25%;
			text-align: center;
			color: #404040;
			background: #FFFFFF;
		}

		.default-active {
			min-width: 25%;
			text-align: center;
			color: #FF4443;
			background: #FFFFFF; 
		}
	}
	//滑动菜单
	.hd-tab {
		display: flex;
		flex-wrap: wrap;
		padding: 0 30upx;
		background: #fff;
	
		.hd-list {
			display: flex;
			flex-direction: column;
			width: 95%;
			padding-bottom: 40upx;
	
			&:nth-child(2n+1) {
				margin-right: 5%;
			}
		}
	
		.image-wrapper {
			width: 100%;
			height: 330upx;
			border-radius: 3px;
			overflow: hidden;
	
			image {
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}
	
		.name {
			font-size: $font-lg;
			color: $font-color-dark;
			line-height: 80upx;
		}
	
		.time{
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
		}
		.kind{
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
		}
		.distance{
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
		}
		
	}
	
</style>
