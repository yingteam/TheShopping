<template>
	<view class="content"> 
		<view class="navbar" :style="{position:headerPosition,top:headerTop}">
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
</template>

<script>
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	export default {
		components: {
			uniLoadMore	
		},
		data() {
			return {
				headerPosition:"fixed",
				headerTop:"0px",
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
		onLoad(o){
			const data = JSON.parse(o.data);
			uni.setNavigationBarTitle({
				title: data.text
			});
			// #ifdef H5
			this.headerTop = async () => document.getElementsByTagName('uni-page-head')[0].offsetHeight+'px';
			// #endif
			this.loadData();
		},
		onPageScroll(e){
			//兼容iOS端下拉时顶部漂移
			if(e.scrollTop>=0){
				this.headerPosition = "fixed";
			}else{
				this.headerPosition = "absolute";
			}
		},
		//下拉刷新
		onPullDownRefresh(){
			this.loadData('refresh');
		},
		//加载更多
		onReachBottom(){
			this.loadData();
		},
		methods: {
			//加载商品 ，带下拉刷新和上滑加载
			async loadData(type='add', loading) {
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
					scrollTop: 0
				});
				this.loadData('refresh', 1);
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
	page, .content{
		background: $page-color-base;
	}
	.content{
		padding-top: 96upx;
	}

	.navbar{
		position: fixed;
		left: 0;
		top: var(--window-top);
		display: flex;
		width: 100%;
		height: 80upx;
		background: #fff;
		box-shadow: 0 2upx 10upx rgba(0,0,0,.06);
		z-index: 10;
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
		.p-box{
			display: flex;
			flex-direction: column;
			.yticon{
				display: flex;
				align-items: center;
				justify-content: center;
				width: 30upx;
				height: 14upx;
				line-height: 1;
				margin-left: 4upx;
				font-size: 26upx;
				color: #888;
				&.active{
					color: $base-color;
				}
			}
			.xia{
				transform: scaleY(-1);
			}
		}
		.cate-item{
			display: flex;
			justify-content: center;
			align-items: center;
			height: 100%;
			width: 80upx;
			position: relative;
			font-size: 44upx;
			&:after{
				content: '';
				position: absolute;
				left: 0;
				top: 50%;
				transform: translateY(-50%);
				border-left: 1px solid #ddd;
				width: 0;
				height: 36upx;
			}
		}
	}

	/* 分类 */
	.cate-mask{
		position: fixed;
		left: 0;
		top: var(--window-top);
		bottom: 0;
		width: 100%;
		background: rgba(0,0,0,0);
		z-index: 95;
		transition: .3s;
		
		.cate-content{
			width: 630upx;
			height: 100%;
			background: #fff;
			float:right;
			transform: translateX(100%);
			transition: .3s;
		}
		&.none{
			display: none;
		}
		&.show{
			background: rgba(0,0,0,.4);
			
			.cate-content{
				transform: translateX(0);
			}
		}
	}
	.cate-list{
		display: flex;
		flex-direction: column;
		height: 100%;
		.cate-item{
			display: flex;
			align-items: center;
			height: 90upx;
			padding-left: 30upx;
 			font-size: 28upx;
			color: #555;
			position: relative;
		}
		.two{
			height: 64upx;
			color: #303133;
			font-size: 30upx;
			background: #f8f8f8;
		}
		.active{
			color: $base-color;
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
			margin-bottom: 20upx;
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
</style>
