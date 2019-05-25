<template>
	<view class="content"> 
		<!--<input type="text">-->
		<view class="xinxi-view">
			<text class="list-tab">基本信息</text>
			<view class="list-block">
				<view class="list-content">
					<text class="list-lable">店铺名称</text>
					<text class="mast">*</text>
					<view class="Item-input">
						<input type="text" placeholder="请输入店铺名称" id="storename" name="merchant[storename]" value="">
					</view>
				</view>
				<view class="list-content">
					<text class="list-lable">店铺电话</text>
					<text class="mast">*</text>
					<view class="Item-input">
						<input type="tel" placeholder="请输入店铺电话" id="tel" name="merchant[mobile]" value="">
					</view>
				</view>
				<view class="list-content">
					<text class="list-lable">行业分类</text>
					<view class="Item-input">
						<input type="text" placeholder="" id="cate-picker" name="cate-picker" value="" readonly="">
					</view>
				</view>
				<view class="list-content">
					<text class="list-lable">营业时间</text>
					<view class="Item-input">
						<view class="tab" :class="[{'active':index==tabIndex}]" @tap="toggleTab(index=3)" >{{this.lastval[this.index]}}</view>
						<wpicker :mode="mode" step="1" :defaultVal="defaultVal" @confirm="onConfirm" ref="picker" themeColor="#f00" ></wpicker>
					</view> 
				</view>
			</view> 
			<text class="list-tab">区域地址</text>
			<view class="list-block">
				<view class="list-content">
					<text class="list-lable">所属区域</text>
					<view class="Item-input">
						<view class="tab"  @tap="toggleTab(index=4)" >{{val}}</view>
						<wpicker :mode="mode" step="1" :defaultVal="defaultVal" @confirm="onConfirm" ref="picker" themeColor="#f00"></wpicker>
					</view>
				</view>
				<view class="list-content">
					<text class="list-lable">详细地址</text>
					<view class="Item-input">
						<input type="text" placeholder="请输入店铺地址" id="storeaddr" name="merchant[address]" value="">
					</view>
					<view class="address-icon">定位
					</view>
				</view>
			</view>
			<text class="list-tab">店铺图片</text>
			<template>
				<view class="list-block">
					<view class="league-icon-view">
						<view class="league-icon">
							<text class="upload-lable">店铺图标(200X200)</text>
							<uniuploadImg  ulr="" :count="1" :length="1" @tap="chooseImage"></uniuploadImg>
						</view> 
					</view>
					<view class="league-icon-view">
						<view class="league-icon">
							<text class="upload-lable">店铺幻灯片(720X300)</text>
							<uniuploadImg  ulr="" :count="3" :length="3" @tap="chooseImage"></uniuploadImg>
						</view>
					</view>
					<view class="league-icon-view">
						<view class="league-icon">
							<text class="upload-lable">店铺相册(320X320)</text>
							<uniuploadImg  ulr="" :count="9" :length="9" @tap="chooseImage"></uniuploadImg>
						</view>
					</view>
					<view class="league-icon-view">
						<view class="league-icon">
							<text class="upload-lable">商家微信(微信二维码)</text>
							<uniuploadImg  ulr="" :count="1" :length="1" @tap="chooseImage"></uniuploadImg>
						</view>
					</view>
				</view>
			</template>
			<text class="list-tab">其他信息</text>
			<template>
				<view class="list-block">
					<view class="league-icon-view">
						<view class="league-icon">
							<view class="Item-input">
								<textarea name="merchant[introduction]" placeholder="请输入店铺介绍文字" style="height: 5rme;"></textarea>
							</view>
							<uniuploadImg  ulr="" :count="3" :length="3" @tap="chooseImage"></uniuploadImg>
						</view>
					</view> 
				</view>
			</template>
			<text class="list-tab">店主信息</text>
			<view class="list-block">
				<view class="list-content">
					<text class="list-lable">店主姓名</text>
					<text class="mast">*</text>
					<view class="Item-input">
						<input type="text" placeholder="请输入店主姓名" class="" id="realname" name="merchant[realname]" value="">
					</view>
				</view>
				<view class="list-content">
					<text class="list-lable">电话</text>
					<text class="mast">*</text>
					<view class="Item-input">
						<input type="tel" placeholder="请输入店主电话" class="" id="tel" name="merchant[tel]" value="">
					</view>
				</view>
			</view>
			<text class="list-tab">付费入驻</text>
			<view class="list-block">
				<view class="list-content">
					<view class="radio">
						<radio checked="true"></radio>
					</view>
					<text class="list-lable" style="width: 100%;text-align: left;">商业版(￥1000/365天)</text>
				</view>
			</view>
			<view class="agree">
				<view class="radio">
					<radio checked="true" ></radio>
					<text class="list-lable" style="width: 100%;text-align: left;;">阅读并同意
						<a href="">《商家入驻相关条款》</a>
					</text>
				</view>
			</view>
			<button  type="warn"  class="button" @click="submit">
				提交
			</button>
		</view>
	</view>
</template>

<script>
	import uniuploadImg from '../../components/upload-images.vue'
	import wpicker from '../../components/w-picker/w-picker.vue'
	export default {
		components:{
			uniuploadImg,
			wpicker
			},
		data() {
			return {
				index:0,
				imageList: [],
				val:'请选择',
				lastval:[],
				upData: {
					title: '',
					content:''
				},
				tabList:[{
					mode:"date",
					name:"日期选择",
					value:[0,1,0]
				},{
					mode:"yearMonth",
					name:"年月",
					value:[0,1]
				},{
					mode:"dateTime",
					name:"日期时间选择",
					value:[1,1,1,1,2,5]
				},{
					mode:"time",
					name:"时间选择",
					value:[0,0,0,0]
				},
				{
					mode:"region",
					name:"省市区",
					value:[10,0,5]
				}],
				tabIndex:0
			}
		},
		computed:{
			mode(){
				return this.tabList[this.tabIndex].mode
			},
			defaultVal(){
				return this.tabList[this.tabIndex].value
			}
		},
		methods: {
			chooseImg() { //选择图片
				uni.chooseImage({
					sourceType: ["camera", "album"],
					sizeType: ["compressed"],
					count: 9,
					success: (res) => {
						this.imageList = res.tempFilePaths;
						for(var i=0;i<res.tempFilePaths.length;i++){
							console.log("第"+i+"张图片路径"+res.tempFilePaths[i])
						}
						console.log(imageList)
					},
				})
			},
			previewImage(current) { //预览图片
				uni.previewImage({
					current,
					urls: this.imageList
				});
			},
			toggleTab(index){
				this.tabIndex=index;
				this.index=index;
				this.$refs.picker.show();
			},
			onConfirm(val){
				this.lastval[this.index]=val.result;
				console.log(this.lastval[this.index]);
				console.log(val);
			}
		}
	}
</script>

<style lang="scss">
	a {
		text-decoration: none;
	}

	.xinxi-view {
		position: relative;
		left: 0;
		width: 100%;
		background: #efeff4;

		.list-tab {
			font-size: .6rem;
			margin-top: .77em;
			margin-bottom: .3em;
			padding-left: 15px;
			padding-right: 15px;
			color: #999999;
		}

		.list-block {
			font-size: .8rem;
			background: #FFFFFF;

			.list-content {
				min-height: 2.2rem;
				display: flex;
				align-items: center;

				.list-lable {
					text-align: center;
					width: 35%;
					vertical-align: top;
					position: relative;
				}

				.mast {
					color: #FF0000;
					text-align: left;
				}

				.Item-input {
					width: 100%
				}

				.address-icon {
					display: flex;
					//max-height: 1.4rem;
					font-size: .7rem;
					white-space: nowrap;
					color: #FF0000;
				}

				.address-icon:before {
					font-size: 16px;
					content: "\e651";
				}
			}

			.league-icon-view {
				padding: 10px 15px;
				position: relative;
				display: flex;
				align-items: center;

				.league-icon {
					.upload-lable {
						display: block;
						margin-block-start: 1em;
						margin-block-end: 1em;
						margin-inline-start: 0px;
						margin-inline-end: 0px;
					}

					.uploadImg-view {
						float: left;
						position: relative;
						margin-right: 9px;
						margin-bottom: 9px;
						width: 77px;
						height: 77px;
						border: 1px solid #D9D9D9;
					}

					.uploadImg-view:before {
						width: 2px;
						height: 39.5px;
					}

					.uploadImg-view:after {
						width: 39.5px;
						height: 2px;
					}

					.uploadImg-view:before,
					.uploadImg-view:after {
						content: " ";
						position: absolute;
						top: 50%;
						left: 50%;
						-webkit-transform: translate(-50%, -50%);
						transform: translate(-50%, -50%);
						background-color: #D9D9D9;
					}

					.uploadImg-view input {
						position: absolute;
						width: 100%;
						height: 100%;
						opacity: 0;
					}

					.uploadImg_unsupportedTips {
						color: red;
						font-size: .75rem;
					}

					.uploadImg_images {
						float: left;
						margin-right: 9px;
						margin-bottom: 9px;
						width: 79px;
						height: 79px;
						background: no-repeat center center;
						background-size: cover;
					}

					.uploadImg_images img {
						width: 100%;
						height: 100%;
					}
				}

			}
		}
		.uni-uploader__file {
			margin: 0 5upx 10upx;
		}
		.uni-uploader__img {
			width: 156upx;
			height: 156upx;
		}
	}
	.tab{
		text-align: center;
		padding:20upx 0;
		font-size: 35upx;
	}
	.tab.active{
		color:#f00;
	}
	
</style>
