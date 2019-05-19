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
				<swiper-item v-for="(item, index) in carouselList" :key="index" class="carousel-item" @click="navToDetailPage(item)">
					<image :src="item.src" />
				</swiper-item>
			</swiper>
		</view>
		<!--二级菜单-->
		<view class="cate-section">
			<view class="cate-item" v-for="(navItem, index) in navList" :key="index" @click="navigateTo(navItem)">
				<image :src="navItem.image"></image>
				<text v-text="navItem.text"></text>
			</view>
		</view>
		<!-- 商家视图 -->
		<view class="shop-view m-t relative">
			<view class="navbar" :class="navFixed ? 'nav-fixed' : 'nav-absolute'">
				<view class="nav-item" v-for="(filterItem, index) in filterList" :key="index"
				  :class="{current: filterIndex === index}" @click="tabClick(index)">
					<text v-text="filterItem.text"></text>
				</view>
			</view>
			<view class="shop-list">
				<!-- 商家列表 -->
				<view class="shop-item" v-for="(item,index) in shopList" :key="index" @click="navToDetailPage(item)" >
					<view class="top-view">
						<image class="shop-img" :src="item.image" mode="aspectFill"></image>
						<view class="shop-info">
							<view class="shop-header">
								<text class="title clamp" v-text="item.name"></text>
								<view class="star">
									<text v-for="star in item.stars" :key="star">
									    <i class="fa fa-star" aria-hidden="true"></i>
									</text>
								</view>
							</view>
							<text>{{ item.openTime }} 营业</text>
							<text v-text="item.category"></text>
							<view class="shop-address">
								<text class="map-marker">
									<i class="fa fa-map-marker" aria-hidden="true"></i>
								</text>
								<text class="address clamp">{{ item.address }}</text>
								<text class="distance">1.21km</text>
							</view>
						</view>
					</view>
					<view class="bottom-view">
						<view class="act-item">
							<text class="act-marker">积</text>
							<text class="clamp act-content">100元积10分</text>
						</view>
					</view>
				</view>
			</view>
			<uni-load-more :status="loadingType"></uni-load-more>
		</view>
	</view>
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	export default {
		components: {
			uniLoadMore	
		},
		data() {
			return {
				titleNViewBackground: '',
				swiperCurrent: 0,
				swiperLength: 0,
				timeIndex: 0,
				carouselList: [],
				newList: [],
				merchantList:[],
				navList: [
					{text: '批发供货', image: '/static/icons/bianmin.jpg', link: '/pages/leagueList/list'},
					{text: '餐饮美食', image: '/static/icons/cm.png', link: '/pages/leagueList/list'},
					{text: '休闲娱乐', image: '/static/icons/xy.png', link: '/pages/leagueList/list'},
					{text: '美容美发',image: '/static/icons/mr.png',link: '/pages/leagueList/list'},
					{text: '购物商超',image: '/static/icons/pintuan.jpg',link: '/pages/leagueList/list'},
					{text: '教育培训',image: '/static/icons/book.png',link: '/pages/leagueList/list'},
					{text: '服装鞋帽',image: '/static/icons/xie.png',link: '/pages/leagueList/list'},
					{text: '近郊旅游',image: '/static/icons/ly.png',link: '/pages/leagueList/list'},
					{text: '装饰建材',image: '/static/icons/ju.png',link: '/pages/leagueList/list'},
					{text: '汽车服务',image: '/static/icons/car.png',link: '/pages/leagueList/list'}
				],
				navFixed: false,
				loadingType: 'more', //加载更多状态
				filterList: [
					{text: '附近'},
					{text: '推荐'},
					{text: '人气'},
					{text: '最新'}
				],
				filterIndex: 0, 
				shopList: []
			};
		},
		onLoad() {
			this.loadCaroData();
			this.loadShopData();
		},
		onNavigationBarButtonTap() {
			this.$api.msg('点击了加盟');
		},
		onPageScroll(e){
			if(e.scrollTop >= 380){
				this.navFixed = true;
			}else{
				this.navFixed = false;
			}
		},
		//加载更多
		onReachBottom(){
			this.loadShopData();
		},
		methods: {
			async loadCaroData () {
				let carouselList = await this.$api.json('carouselList');
				this.titleNViewBackground = carouselList[0].background;
				this.swiperLength = carouselList.length;
				this.carouselList = carouselList;
			},
			//轮播图切换修改背景色
			swiperChange (e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				this.titleNViewBackground = this.carouselList[index].background;
			},
			navigateTo (nav) {
				let url = nav.link;
				url += '?data=' + JSON.stringify(nav);
				uni.navigateTo({ url });
			},
			//加载商品 ，带下拉刷新和上滑加载
			async loadShopData (type='add', loading) {
				//没有更多直接返回
				if(type === 'add'){
					if(this.loadingType === 'nomore'){
						return;
					}
					this.loadingType = 'loading';
				}else{
					this.loadingType = 'more';
				}
				
				let shopList = await this.$api.json('shopList');
				if(type === 'refresh'){
					this.shopList = [];
				}
				this.shopList = this.shopList.concat(shopList);
				
				//判断是否还有下一页，有是more  没有是nomore(测试数据判断大于20就没有了)
				this.loadingType  = this.shopList.length > 20 ? 'nomore' : 'more';
				if(type === 'refresh'){
					if (loading == 1) {
						uni.hideLoading()
					} else {
						uni.stopPullDownRefresh();
					}
				}
			},
			//筛选点击
			tabClick(index){
				if(this.filterIndex === index) return;
				this.filterIndex = index;
				uni.pageScrollTo({
					duration: 300,
					scrollTop: 380
				});
				this.loadShopData('refresh', 1);
				uni.showLoading({
					title: '正在加载'
				});
			},
			//详情
			navToDetailPage(item){
				//测试数据没有写id，用name代替
				let id = item.name;
				uni.navigateTo({
					url: `/pages/detail/detail?id=${id}`
				});
			}
		}
	}
</script>

<style lang="scss">
	/* #ifdef MP */
	.mp-search-box{
		position:absolute;
		left: 0;
		top: 30upx;
		z-index: 9999;
		width: 100%;
		padding: 0 80upx;
		.ser-input{
			flex:1;
			height: 56upx;
			line-height: 56upx;
			text-align: center;
			font-size: 28upx;
			color:$font-color-base;
			border-radius: 20px;
			background: rgba(255,255,255,.6);
		}
	}
	page{
		.cate-section{
			position:relative;
			z-index:5;
			border-radius:16upx 16upx 0 0;
			margin-top:-20upx;
		}
		.carousel-section{
			padding: 0;
			.titleNview-placing {
				padding-top: 0;
				height: 0;
			}
			.carousel{
				.carousel-item{
					padding: 0;
				}
			}
			.swiper-dots{
				right:45upx;
				bottom:40upx;
			}
		}
	}
	/* #endif */
	
	page {
		background: #f5f5f5;
	}
	.m-t{
		margin-top: 20upx;
	}
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

	.shop-view {
		padding-top: 82upx;
		.nav-absolute {
			position: absolute;
			top: 0;
			left: 0;
		}
		.nav-fixed {
			position: fixed;
			top: 44px;
			left: 0;
			z-index: 10;
		}
		.navbar{
			display: flex;
			width: 100%;
			height: 80upx;
			background: #fff;
			box-shadow: 0 2upx 10upx rgba(0,0,0,.06);
			.nav-item{
				flex: 1;
				display: flex;
				justify-content: center;
				align-items: center;
				height: 100%;
				font-size: 30upx;
				color: $font-color-dark;
				position: relative;
				&.current{
					color: $base-color;
					&:after{
						content: '';
						position: absolute;
						left: 50%;
						bottom: 0;
						transform: translateX(-50%);
						width: 120upx;
						height: 0;
						border-bottom: 4upx solid $base-color;
					}
				}
			}
		}
		/* 商铺列表 */
		.shop-list{
			display:flex;
			flex-direction: column;
			font-size: $font-base;
			color: $font-color-light;
			.shop-item{
				padding: 24upx 30upx;
				margin-bottom: 2upx;
				background: #fff;
				.top-view {
					display: flex;
					.shop-img {
						width: 180upx;
						height: 180upx;
						min-width: 180upx;
						border-radius: 10upx;
					}
					.shop-info {
						flex: 1;
						max-width: 74%;
						display: flex;
						flex-direction: column;
						padding-top: 10upx;
						padding-left: 20upx;
						.shop-header,
						.shop-address {
							display: flex;
							.title {
								flex: 1;
								font-size: $font-lg;
								color: $font-color-dark;
							}
							.map-marker {
								color: $font-color-spec;
							}
							.address {
								flex: 1;
								margin-left: 12upx;
							}
							.star,
							.distance {
								margin-left: 24upx;
								color: $uni-color-primary;
							}
						}
					}
				}
				.bottom-view {
					padding-top: 10upx;
					.act-item {
						display: flex;
						.act-marker {
							width: 1rem;
							height: 1rem;
							line-height: 1rem;
							text-align: center;
							color: #ffffff;
							background: $uni-color-primary;
							border-radius: 8upx;
						}
						.act-content {
							flex: 1;
							margin-left: 12upx;
						}
					}
				}
			}
		}
	}
</style>
