<template>
	<view>
		<view class="search-box">
			<my-search @click="gotoSearch"></my-search>
		</view>

		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, index) in swiperList" :key="index">
				<navigator class="swiper-item" :url="'/subpkg/goods_datail/goods_detail?goods_id=' + item.goods_id">
					<image src="item.image_src"></image>
				</navigator>
			</swiper-item>
		</swiper>

		<view class="nav-list">
			<view class="nav-item" v-for="(item, index) in navList" :key="index" @click="navClickHandler(item)">
				<image :src="item.image.src" class="nav-img"></image>
			</view>
		</view>

		<view class="floor-list">
			<view class="floor-item" v-for="(item, index) in floorList" :key="index">
				<image :src="item.floor_title.image_src" class="floor-title"></image>

				<view class="floor-img-box">
					<navigator class="left-img-box" :url="item.product_list[0].url">
						<image :src="item.product_list[0].image_src" mode="widthFix" :style="{ width: item.product_list[0].image_width + 'rpx' }"></image>
					</navigator>

					<view class="right-img-box">
						<navigator class="right-img-item" v-for="(item2, index2) in item.product_list" :key="index2" v-if="index2 !== 0" :url="item2.url">
							<image :src="item2.image_src" mode="widthFix" :style="{ width: item2.image_width + 'rpx' }"></image>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import badgeMix from '@/mixins/tabbar-badge.js'
export default {
	mixins: [badgeMix],
	data() {
		return {
			swiperList: [],
			navList: [],
			floorList: []
		};
	},
	methods: {
		async getSwiperList() {
			const {
				data: res
			} = await uni.$http.get('/api/public/v1/home/swiperdate')
			if (res.meta.status !== 200) {
				return uni.$showMsg()
			}
			this.swiperList = res.message
		},
		async getNavList() {
			const {
				data: res
			} = await uni.$http.get('/api/public/v1/home/catitems')
			if (res.meta.status !== 200) {
				return uni.$showMsg()
			}
			this.navList = res.message
		},
		async getFloorList() {
			const {
				data: res
			} = await uni.$http.get('/api/public/v1/home/floordata')

			res.message.forEach(floor => {
				floor.product_list.forEach(prod => {
					prod.url = '/subpkg/goods_list/goods_list?' + prod.navigator_url.split('?')[1]
				})
			})

			if (res.meta.status !== 200) {
				return uni.$showMsg()
			}
			this.floorList = res.message
		},
		navClickHandler(item) {
			if (item.name === '分类') {
				uni.switchTab({
					url: '/pages/cate/cate'
				})
			}
		},
		gotoSearch() {
		  uni.navigateTo({
		    url: '/subpkg/search/search'
		  })
		}
	}
},
onLoad() {
	this.getSwiperList()
	this.getNavList()
	this.getFloorList()
}
}
</script>

<style lang="scss">
swiper {
	height: 330rpx;

	.swiper-item,
	image {
		width: 100%;
		height: 100%;
	}
}

.nav-list {
	display: flex;
	justify-content: space-around;
	margin: 15px 0;

	.nav-img {
		width: 128rpx;
		height: 140rpx;
	}
}

.floor-title {
	width: 100%;
	height: 60rpx;
}

.right-img-box {
	display: flex;
	flex-wrap: wrap;
	justify-content: space-around;
}

.floor-img-box {
	display: flex;
	padding-left: 14rpx;
}
.search-box {
	position: sticky;
	top: 0;
	z-index: 999;
}
</style>
