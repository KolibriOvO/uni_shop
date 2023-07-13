<template>
	<view>
		<view class="cart-title">
			<uni-icons type="shop" size="18"></uni-icons>
			<text class="cart-title-text">购物车</text>
		</view>

		<block v-for="(goods, i) in cart" :key="i">
			<my-goods :goods="goods" :show-radio="true" :show-num="true" @radio-change="radioChangeHandler"
				@num-change="numberChangeHandler"></my-goods>
		</block>
	</view>
</template>

<script>
	import badgeMix from '@/mixins/tabbar-badge.js';
	import {
		mapState,
		mapMutations
	} from 'vuex';

	export default {
		mixins: [badgeMix],
		data() {
			return {};
		},
		methods: {
			...mapMutations('m_cart', ['updateGoodsState']),
			radioChangeHandler(e) {
				this.updateGoodsState(e)
			},
			numberChangeHandler(e) {
				console.log(e)
			}
		},
		computed: {
			...mapState('m_cart', ['cart'])
		}
	};
</script>

<style lang="scss">
	.cart-title {
		height: 40px;
		display: flex;
		align-items: center;
		font-size: 14px;
		padding-left: 5px;
		border-bottom: 1px solid #efefef;

		.cart-title-text {
			margin-left: 10px;
		}
	}
</style>