<template>
	<view class="content">
		<empty v-if="list && list.length <= 6"></empty>
		<view class="list">
			<view class="item" v-for="(item, index) in list" :key="index" v-if="item.product != null">
				<view class="image-box" @click="toDetail(item)">
					<image class="image" :src="item.product.image[0]"></image>
				</view>
				<view class="text" @click="toDetail(item)">
					<text class="title">{{item.product.title}}</text>
					<text class="price">￥{{item.product.price}}</text>
				</view>
				<view class="deleteBtn">
					<text class="del-btn iconfont iconshanchu1" @click="deleteFavorite(item, index)"></text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import empty from "@/components/empty";
	import {
		mapActions,
		mapState
	} from 'vuex';
	export default {
		components: {
			empty
		},
		data() {
			return {
				list: [],
				product: {},
				quantity: 1,
				id: null,
				attrChoose: {},
			}
		},
		onLoad() {
			this.getList()
		},
		computed: {
			currentSku() {
				let attrValues = []
				for (let i in this.attrChoose) {
					attrValues.push(this.product.attrItems[i][this.attrChoose[i]])
				}
				console.log(attrValues);
				const val = attrValues.join(',');
				for (let i in this.product.skus) {
					if (this.product.skus[i].value == val) return this.product.skus[i]
				}
			}
		},
		methods: {
			...mapActions(['getProductInfo', 'cartAddProduct', 'getCartProducts']),
			getList() {
				return this.$http.get('user.favorite').then(res => {
					this.list = res.data;
					console.log(this.list);
				}).catch(e => {})
			},
			toDetail(item) {
				uni.navigateTo({
					url: '/pages/product/product?id=' + item.product.id
				})
			},
			deleteFavorite(item, index) {
				let form = {
					id: item.product.id,
					state: item.product.favorite = 0
				}
				this.$http.post('product.favorite', form).then(res => {
					this.$set(item.product, 'favorite', form.state)
					this.getList();
				})
			},
		},
		onPullDownRefresh() {
			this.getList().then(res => {
				uni.stopPullDownRefresh()
			}).catch(e => {
				uni.stopPullDownRefresh()
			})
		}
	}
</script>

<style lang="scss">
	.list {
		display: flex;
		flex-direction: column;
		padding: 0 10upx;

		.item {
			display: flex;
			overflow: hidden;
			margin: 10upx 0;

			.image-box {
				display: flex;
				height: 200upx;
				width: 200upx;

				.image {
					width: 100%;
					height: 100%;
				}
			}

			.text {
				font-size: $font-base;
				padding: 10px;
				display: flex;
				flex-direction: column;
				flex: 1;

				.title {
					display: -webkit-box;
					-webkit-box-orient: vertical;
					-webkit-line-clamp: 2;
					overflow: hidden;
				}

				.price {
					color: red;
					font-size: $font-base;
					margin: 10upx 0;
				}
			}
		}
	}

	.deleteBtn {
		width: 80upx;
		display: flex;
		align-items: center;
		justify-content: center;
		flex-direction: column;
		// background-color: red;
	}

	.del-btn {
		font-size: 40upx;
	}
</style>
