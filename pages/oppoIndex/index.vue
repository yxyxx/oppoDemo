<template>
	<!-- input -->
	<view class="search_nav">
		<Search style="width: 88%;"></Search>
		<text class="login_btn">
			登录
		</text>
	</view>
	<!-- swiper -->
	<view class="swiper-bannner" @click="clicBanner">
		<uni-swiper-dot class="uni-swiper-dot-box" @clickItem=clickItem :info="indexBannerSwiper" :current="current"
			:mode="mode" :dots-styles="dotsStyles" field="content">
			<swiper class="swiper-box" @change="change" :current="util.swiperDotIndex" autoplay circular interval=3000>
				{{item}}
				<swiper-item v-for="(item, index) in indexBannerSwiper" :key="index">
					<image :src="item.url" mode="heightFix" width="100%"></image>
				</swiper-item>
			</swiper>
		</uni-swiper-dot>
	</view>
	<!-- navsort -->
	<view class="nav-sort">
		<view class="sort-item" v-for="(item) in navSort" :key="item">
			<image :src="item.url" mode="widthFix" style="width:100rpx"></image>
			<view>{{item.title}}</view>
		</view>
	</view>
	<view class="main">
		<view class="floor-panel-contain">
			<image :src="item" mode="widthFix" style="width:48.9%;margin-bottom: 15rpx;"
				v-for="item in floorPanelImages" :key="item">
			</image>
		</view>
		<view class="floor-panel-column-contain">
			<image :src="item" mode="widthFix" style="width:23.5%;" v-for="item in floorPanelColumnImages" :key="item">
			</image>
		</view>
	</view>
	<!-- 今日必抢 -->
	<view class="main todayRob">
		<view class="title">
			<view class="left">
				<text class="titleText">今日必抢 </text>
				<view class="countdown">
					<uni-countdown :show-day="false" :second="(todayRobList.endAt - todayRobList.beginAt)/1000"
						color="#f63434" />
					<text>后结束</text>
				</view>
			</view>
			<view class="more">
				更多
				<uni-icons type="forward" size="10" style="color: #999;"></uni-icons>
			</view>
		</view>
		<view class="goodsList">
			<view class="goodsItem" v-for="item in todayRobList.productDetailss" :key="item">
				<image :src="item.url" mode="widthFix" style="width: 184rpx;"></image>
				<view class="info">
					{{item.title}}
				</view>
				<view class="price">
					<text class="newPrice">{{item.priceInfo.price}}</text>
					<text class="oldPrice">{{item.priceInfo.originalPrice}}</text>
				</view>
			</view>
			<view class="moreGoods">
				<view class="title">更多<br>产品</view>
				<uni-icons type="forward" size="10"
					style="width: 46rpx;height: 46rpx;border-radius: 50%;background-color: #eee;text-align: center;line-height: 46rpx;color: #ccc;margin-top: 20rpx;">
				</uni-icons>
			</view>
		</view>
	</view>
	<!-- 大分类 -->
	<view class="main">
		<view class="sort-pannel" v-for="item in sortPannel" :key="item">
			<view class="title" style="margin-top:20rpx">{{item.name}}</view>
			<image :src="item.url" mode="widthFix" style="width:100%;border-radius: 20rpx;margin: 25rpx 0;">
			</image>
			<view class="sort-item" v-for="itemList in item.productDetailss" :key="itemList">
				<view class="">
					<image :src="itemList.url" mode="widthFix" style="width: 100%;"></image>
					<view class="info">
						{{itemList.goodsSpuName}}
					</view>
				</view>
				<view class="price">
					<text>{{itemList.priceInfo.prefix}}</text>
					{{itemList.priceInfo.buyPrice || itemList.priceInfo.price}}
				</view>
			</view>
		</view>
	</view>
	<!-- 底部 -->
	<Footer></Footer>
	<GoTop :isShow="isShow"></GoTop>
</template>
<script setup>
	import {
		ref,
	} from 'vue'
	import util from './util.js'
	import {
		onLoad,
		onPageScroll
	} from '@dcloudio/uni-app';
	import store from '@/store/index.js';
	store.dispatch('getIndexBannerSwiper')
	// 返回顶部
	onPageScroll((e) => {
		console.log(1111111111);
		if (e.scrollTop > 500) {
			isShow.value = true
		} else {
			isShow.value = false
		}
	})
	const {
		dotStyle,
		dotsStyles,
		floorPanelImages,
		floorPanelColumnImages,
		// sortPannel,
		// todayRobList
	} = util
	import Footer from '@/components/footer.vue'
	import Search from '@/components/search.vue'
	import GoTop from '@/components/goTop.vue'
	const modeIndex = ref(-1)
	const styleIndex = ref(-1)
	const current = ref(0)
	const mode = ref('dot')
	const swiperDotIndex = ref(0)
	const isShow = ref(false)
	const indexBannerSwiper = ref([])
	const change = (e) => {
		current.value = e.detail.current
	}
	const selectStyle = (index) => {
		dotsStyles.value = dotStyle.value[index]
		styleIndex.value = index
	}
	const selectMode = (mode, index) => {
		mode.value = mode
		modeIndex.value = index
		styleIndex.value = -1
		dotsStyles.value = dotStyle.value[0]
	}
	const clickItem = (e) => {
		swiperDotIndex.value = e
	}
	const onBanner = (index) => {
		console.log(22222, index);
	}
	uni.request({
		url: '/api/cn/oapi/configs/web/banners/040101,040201',
		method: 'GET',
		success: res => {
			indexBannerSwiper.value = res.data.data
		}
	})
	// 点击banner跳转
	const clicBanner = () => {
		console.log(current.value);
	}
	// navsort
	const navSort = ref([])
	uni.request({
		url: '/api/cn/oapi/configs/web/icons/040202,040203',
		method: 'GET',
		success: res => {
			navSort.value = res.data.data
		}
	})
	// todayRobList今日必抢
	const todayRobList = ref([])
	const sortPannel = ref([])
	uni.request({
		url: '/api/cn/oapi/goods/web/products/v15/040204',
		method: 'GET',
		success: res => {
			todayRobList.value = res.data.data[0]
			res.data.data.splice(0, 1)
			sortPannel.value = res.data.data
		}
	})
	// 倒计时
	const hour = 1
	const minute = 0
	const second = 0
</script>

<style lang="scss" scoped>
	@import '@/common/iconfont.css';

	.search_nav {
		background-color: #fff;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;

		.login_btn {
			width: 12%;
			text-align: left;
		}
	}

	.swiper-bannner {
		.swiper-box {
			height: 240px;
		}

		.swiper-item {
			/* #ifndef APP-NVUE */
			display: flex;
			/* #endif */
			flex-direction: column;
			justify-content: center;
			align-items: center;
			height: 240px;
			color: #fff;
		}

		::v-deep uni-image {
			width: 100% !important;
		}
	}

	.nav-sort {
		display: flex;
		padding: 30rpx 0;
		background-color: #fff;

		.sort-item {
			width: 25%;
			text-align: center;
			color: #666;
		}
	}

	.main {
		padding: 0 24rpx;
		margin-top: 25rpx;
	}

	.floor-panel-contain {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.floor-panel-column-contain {
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
		margin-top: 15rpx;
	}

	.sort-pannel {
		font-size: 30rpx;
		line-height: 85rpx;
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
		line-height: 40rpx;

		.sort-item {
			background-color: #fff;
			width: 32.5%;
			margin-top: 8rpx;
			border-radius: 10rpx;
			padding: 15rpx 0 30rpx;
			display: flex;
			flex-direction: column;
			justify-content: space-between;
		}

		.info,
		.price {
			padding: 0 15rpx;
		}

		.info {
			font-size: 26rpx
		}

		.price {
			font-size: 26rpx;
			justify-self: flex-end;

			text {
				font-size: 12rpx;

				&::after {
					content: '￥'
				}
			}
		}
	}

	// 今日必抢
	.todayRob {

		.title,
		.left,
		.countdown {
			display: flex;
			justify-content: space-between;
		}

		.title {
			line-height: 60rpx;

			.left {
				.titleText {
					font-size: 32rpx
				}

				.countdown {
					::v-deep .uni-countdown__number {
						font-size: 24rpx !important
					}

					text {
						color: #999;
					}
				}
			}

			.more {
				color: #999;
				font-size: 28rpx;
			}
		}

		.goodsList {
			display: flex;
			flex-wrap: nowrap;
			align-items: center;
			overflow: auto;
			border-radius: 10rpx;
			-ms-overflow-style: none;
			overflow: -moz-scrollbars-none;
			background-color: #fff;
			padding-right: 40rpx;

			&::-webkit-scrollbar {
				width: 0 !important
			}

			.goodsItem {
				width: 208rpx;
				text-align: center;

				.info {
					white-space: nowrap;
					overflow: hidden;
					text-overflow: ellipsis;
					padding: 0 10rpx;
					box-sizing: border-box;
					font-size: 28rpx;
				}

				.price {
					text-align: left;
					padding: 0 10rpx;
					box-sizing: border-box;
					margin: 10rpx 0 0;

					.newPrice,
					.oldPrice {
						&::before {
							content: "￥";
							font-size: 18rpx
						}
					}

					.newPrice {
						font-size: 28rpx;
					}

					.oldPrice {
						font-size: 24rpx;
						color: #999;
					}
				}
			}

			.moreGoods {
				border: 1px solid #ddd;
				border-radius: 10rpx;
				height: 230rpx;
				margin: 40rpx 40rpx 0 40rpx;
				display: flex;
				align-items: center;
				justify-content: center;
				flex-direction: column;

				.title {
					font-size: 30rpx;
					width: 140rpx;
					text-align: center;
					display: block;
					line-height: 35rpx;
					color: #999;
				}

				// .iconfont {
				// 	width: 46rpx;
				// 	height: 46rpx;
				// 	border-radius: 50%;
				// 	background-color: #eee;
				// 	text-align: center;
				// 	line-height: 46rpx;
				// 	color: #ccc;
				// 	margin-top: 20rpx;
				// }
			}
		}
	}
</style>
