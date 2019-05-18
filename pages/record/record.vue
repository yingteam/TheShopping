<template>
	<view class="container">
		<!-- 空白页 -->
		<view v-if="recordList==null" class="empty">
			你还没看过任何商品呢！
			<navigator class="navigator" url="../index/index" open-type="switchTab">随便逛逛></navigator>
		</view>
		<view v-else>
			<view class="record-list">
				<block v-for="(item, index) in recordList" :key="item.id">
					<view class="record-item" :class="{'b-b': index!==recordList.length-1}">
						<view class="image-wrapper">
							<image :src="item.image" 
								:class="[item.loaded]"
								mode="aspectFill" 
								lazy-load 
								@load="onImageLoad('recordList', index)" 
								@error="onImageError('recordList', index)"
							></image>
						</view>
						<view class="item-right">
							<text class="clamp title">{{item.title}}</text>
						</view>
					</view>
				</block>
			</view>
		</view>
	</view>
</template>

<script>
	import {
	mapState
	} from 'vuex';
	export default {
		data() {
			return {
				recordList: [],
			};
		},
		onLoad(){
			this.loadData();
		},
		computed:{
			...mapState(['hasLogin'])
		},
		methods: {
			//请求数据
			async loadData(){
				let list = await this.$api.json('goodsList'); 
				//console.log(list);
				let recordList = list.map(item=>{
					item.checked = true;
					return item;
				});
				this.recordList = recordList;
			},
			//监听image加载完成
			onImageLoad(key, index) {
				this.$set(this[key][index], 'loaded', 'loaded');
			},
			//监听image加载失败
			onImageError(key, index) {
				this[key][index].image = '/static/errorImage.jpg';
			},
		}
	}
</script>

<style lang='scss'>
	.container{
		padding-bottom: 134upx;
	}
	.record-item{
		display:flex;
		position:relative;
		padding:30upx 40upx;
		.image-wrapper{
			width: 230upx;
			height: 230upx;
			flex-shrink: 0;
			background-color: transparent;
			position:relative;
			image{
				border-radius:8upx;
			}
		}
		.item-right{
			display:flex;
			flex-direction: column;
			flex: 1;
			overflow: hidden;
			position:relative;
			padding-left: 30upx;
			.title{
				font-size:$font-base + 2upx;
				color: $font-color-dark;
				height: 40px;
				line-height: 40upx;
			}	
		}
	}
</style>
