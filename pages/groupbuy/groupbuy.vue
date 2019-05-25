<template>
	<view class="content">
		<view class="commodity" v-for="(Item,index) in tabList" :key="index">
			<view class="commodity-tab">
				<view class="tab-view">
					<text class="tab-text">{{Item.text}}</text>
				</view>
			</view>
			<view class="list-box">
				<view class="commodity-list" v-for="(selectItem,index) in commodityList" :key="index">
						<image class="commodity-img" :src="selectItem.image" mode="aspectFill"></image>
						<view class="list-text">
							<text class="shop-name">{{selectItem.title}}</text>
							<view class="group-view">
								<text class="group-num">已团100条</text>
								<text class="people-num">5人团</text>
							</view>
							<view class="price-view">
								<text class="group-price">{{selectItem.price}}</text>
								<text class="group-price last-price">{{selectItem.price}}</text>
							</view>
						</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				commodityList: [],
				tabList: [{
						text: '推荐'
					},
					{
						text: '精选'
					},
					{
						text: '最新'
					},
				],
			};
		},
		onLoad() {
			this.loadData();
		},
		methods: {
			async loadData() {
				let commodityList = await this.$api.json('goodsList');
				this.commodityList = commodityList;
			},
		}

	}
</script>

<style lang="scss">
	.commodity {
		position: relative;
		left: 0;
		width: 100%;
		background: #fff;

		.commodity-tab {
			text-align: center;
			left: 100px;
			height: 40px;
			.tab-view{
				margin-top:15px;
				text-align: center;
				.tab-text{
					color: #ff043a;
				}
			}
		}
		.list-box{
			display:flex;
			flex-direction: column;
			font-size: $font-base;
			color: $font-color-light;
			.commodity-list {
				display: flex;
				padding: 24upx 30upx;
				margin-bottom: 2upx;
				background: #fff;
				.commodity-img {
					width: 180upx;
					height: 180upx;
					min-width: 180upx;
					border-radius: 10upx;
				}
			
				.list-text {
					flex: 1;
					max-width: 74%;
					display: flex;
					flex-direction: column;
					padding-top: 10upx;
					padding-left: 20upx;
					.shop-name{
						font-size: $font-lg;
						color: $font-color-dark;
					}
					.group-view {
						display: flex;
						align-items: center;
						justify-content: space-between;
						margin-top: 2px;
						text-align: left;
						//.group-num{}
						.people-num{
							color: #FF4443;
						}
					}
				}
			
				.price-view {
					display: flex;
					align-items: center;
					justify-content: space-between;
					margin-top: 3px;
					padding-right: 10upx;
					font-size: 24upx;
					color: $font-color-light;
				}
			
				.group-price {
					font-size: $font-lg;
					color: $uni-color-primary;
					line-height: 1;
			
					&:before {
						content: '￥';
						font-size: 26upx;
					}
				}
			
				.last-price {
					color: #999;
					font-size: .7rem;
					font-style: normal;
					text-decoration: line-through;
					margin-left: .5rem;
				}
			}
		}
	}
</style>
