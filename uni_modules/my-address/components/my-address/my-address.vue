<template>
	<view>

		<view class="address-choose-box" v-if="JSON.stringify(address) === '{}'">
			<button type="primary" size="mini" class="btnChooseAddress" @click="chooseAddress">请选择收货地址+</button>
		</view>

		<view class="address-info-box" v-else @click="chooseAddress">
			<view class="row1">
				<view class="row1-left">
					<view class="username">收货人：<text>{{address.userName}}</text></view>
				</view>
				<view class="row1-right">
					<view class="phone">电话：<text>{{address.telNumber}}</text></view>
					<uni-icons type="arrowright" size="16"></uni-icons>
				</view>
			</view>
			<view class="row2">
				<view class="row2-left">收货地址：</view>
				<view class="row2-right">{{addstr}}</view>
			</view>
		</view>

		<image src="/static/cart_border@2x.png" class="address-border"></image>
	</view>
</template>
<script>
	import {
		mapState,
		mapMutations,
		mapGetters
	} from 'vuex'
	export default {
		data() {
			return {}
		},
		methods: {
			...mapMutations('m_user', ['updateAddress']),
			async chooseAddress() {
				const [error, success] = await uni.chooseAddress().catch(error => error)

				if (error === null && success.errMsg === 'chooseAddress:ok') {
					this.updateAddress(success)
				}
			}
		},
		computed: {
			...mapState('m_user', ['address']),
			...mapGetters('m_user', ['addstr'])
		}
	}
</script>
<style lang="scss">
	.address-border {
		display: block;
		width: 100%;
		height: 5px;
	}

	.address-choose-box {
		height: 90px;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.address-info-box {
		font-size: 12px;
		height: 90px;
		display: flex;
		flex-direction: column;
		justify-content: center;
		padding: 0 5px;

		.row1 {
			display: flex;
			justify-content: space-between;

			.row1-right {
				display: flex;
				align-items: center;

				.phone {
					margin-right: 5px;
				}
			}
		}

		.row2 {
			display: flex;
			align-items: center;
			margin-top: 10px;

			.row2-left {
				white-space: nowrap;
			}
		}
	}
</style>