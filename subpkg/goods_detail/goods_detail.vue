<template>
	<view v-if="goods_info.goods_name">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :circular="true">
			<swiper-item v-for="(item, index) in goods_info.pics" :key="index">
				<image :src="item.pics_big" @click="preview(index)"></image>
			</swiper-item>
		</swiper>

		<view class="goods-info-box">
			<view class="price">￥{{ goods_info.goods_price }}</view>
			<view class="goods-info-body">
				<view class="goods-name">{{ goods_info.goods_name }}</view>
				<view class="favi">
					<uni-icons type="star" size="18" color="gray"></uni-icons>
					<text>收藏</text>
				</view>
			</view>
			<view class="express">快递：免运费</view>
		</view>

		<rich-text :nodes="goods_info.goods_introduce"></rich-text>

		<view class="goods_nav">
			<uni-goods-nav :fill="true" :options="options" :buttonGroup="buttonGroup" @click="onClick" @buttonClick="buttonClick" />
		</view>
	</view>
</template>

<script>
import {
	mapState,
	mapMutations,
	mapGetters
} from 'vuex'
import {
	computed
} from "vue"


export default {
	data() {
		return {
			goods_info: {},
			options: [{
				icon: 'shop',
				text: '店铺'
			}, {
				icon: 'cart',
				text: '购物车',
				info: 2
			}],
			buttonGroup: [{
					text: '加入购物车',
					backgroundColor: '#ff0000',
					color: '#fff'
				},
				{
					text: '立即购买',
					backgroundColor: '#ffa200',
					color: '#fff'
				}
			]
		}
	};
},
methods: {
		...mapMutations('m_cart', ['addToCart']),
		async getGoodsDetail(goods_id) {
			const {
				data: res
			} = await uni.$http.get('/api/public/v1/goods/detail', {
				goods_id
			})
			if (res.meta.status !== 200) return uni.$showMsg()

			res.message.goods_introduce = res.message.goods_introduce.replace(/<img /g, '<img style="display:block;" ')
				.replace(/webp/g, 'jpg')
			this.goods_info = res.message
		},
		preview(index) {
			uni.previewImage({
				current: index,
				urls: this.goods_info.pics.map(item => item.pics_big)
			})
		},
		onClick(e) {
			if (e.content.text === '购物车') {
				uni.switchTab({
					url: '/pages/cart/cart'
				})
			}
		},
		buttonClick(e) {
			// 1. 判断是否点击了 加入购物车 按钮
			if (e.content.text === '加入购物车') {

				// 2. 组织一个商品的信息对象
				const goods = {
					goods_id: this.goods_info.goods_id, // 商品的Id
					goods_name: this.goods_info.goods_name, // 商品的名称
					goods_price: this.goods_info.goods_price, // 商品的价格
					goods_count: 1, // 商品的数量
					goods_small_logo: this.goods_info.goods_small_logo, // 商品的图片
					goods_state: true // 商品的勾选状态
				}

				// 3. 通过 this 调用映射过来的 addToCart 方法，把商品信息对象存储到购物车中
				this.addToCart(goods)

			}
		}
	},
	computed() {
		...mapGetters('m_cart', ['total']),
	},
	watch: {
		// 定义 total 侦听器，指向一个配置对象
		total: {
			// handler 属性用来定义侦听器的 function 处理函数
			handler(newVal) {
				const findResult = this.options.find(x => x.text === '购物车')
				if (findResult) {
					findResult.info = newVal
				}
			},
			// immediate 属性用来声明此侦听器，是否在页面初次加载完毕后立即调用
			immediate: true
		}
	}
onLoad(options) {
	const goods_id = options.goods_id
	this.getGoodsDetail(goods_id)
}
}
</script>

<style lang="scss">
swiper {
	height: 750rpx;

	image {
		width: 100%;
		height: 100%;
	}
}

.goods-info-box {
	padding: 10px;
	padding-right: 0;

	.price {
		color: #c00000;
		font-size: 18px;
		margin: 10px 0;
	}

	.goods-info-body {
		display: flex;
		justify-content: space-between;

		.goods-name {
			font-size: 13px;
			padding-right: 10px;
		}

		.favi {
			width: 120px;
			font-size: 12px;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			border-left: 1px solid #efefef;
			color: gray;
		}
	}

	.express {
		margin: 10px 0;
		font-size: 12px;
		color: gray;
	}
}

.goods-detail-container {
	padding-bottom: 50px;
}

.goods_nav {
	position: fixed;
	bottom: 0;
	left: 0;
	width: 100%;
}
</style>
