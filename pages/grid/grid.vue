<template>
	<view class="warp">
		<!-- #ifdef APP-PLUS -->
		<status-bar />
		<!-- #endif -->
		<!-- banner -->
		<unicloud-db ref="bannerdb" v-slot:default="{data, loading, error, options}" collection="opendb-banner"
			field="_id,bannerfile,open_url,title" @load="load">
			<uni-swiper-dot class="uni-swiper-dot-box" @clickItem="clickItem" :info="data || bannerFormate(data, loading)" 
				:current="current" field="content">
				<swiper class="swiper-box" @change="changeSwiper" :current="swiperDotIndex">
					<swiper-item v-for="(item, index) in (data || bannerFormate(data, loading))" :key="item._id">
						<view :draggable="false" class="swiper-item" @click="clickBannerItem(item)">
							<image class="swiper-image" :src="item.bannerfile.url" mode="aspectFill" :draggable="false" />
						</view>
					</swiper-item>
				</swiper>
			</uni-swiper-dot>
		</unicloud-db>
	<!-- 宫格 -->
		<uni-section title="宫格组件" style="margin: 0;" type="line"></uni-section>
		<view class="example-body">
			<uni-grid :column="3" :highlight="true" @change="change">
				<template v-for="(item,i) in gridList">
					<uni-grid-item :index="i" :key="i"
						v-if="i<3 || i>2&&i<6&&hasLogin || i>5&&uniIDHasRole('admin')"
					>
						<view class="grid-item-box" style="background-color: #fff;">
							<image :src="'/static/grid/c'+(i+1)+'.png'" class="image" mode="aspectFill" />
							<text class="text">{{item}}</text>
						</view>
					</uni-grid-item>
				</template>
			</uni-grid>
		</view>
	</view>
</template>

<script>
	import {
		mapGetters,
	} from 'vuex';
	import statusBar from "@/uni_modules/uni-nav-bar/components/uni-nav-bar/uni-status-bar";
	export default {
		components: {
			statusBar
		},
		data() {
			return {
				gridList: ['所有人可见','所有人可见','所有人可见', '游客不可见', '游客不可见', '游客不可见','管理员可见','管理员可见','管理员可见'],
				current: 0,
				swiperDotIndex: 0
			}
		},
		computed: {
			...mapGetters({
				hasLogin: 'user/hasLogin'
			})
		},
		methods: {
			change(e) {
				uni.showToast({
					title: `点击第${e.detail.index}个宫格`,
					icon: 'none'
				})
			},
			/**
			 * banner加载后触发的回调
			 */
			load(data) {

			},
			changeSwiper(e) {
				this.current = e.detail.current
			},
			clickItem(e) {
				this.swiperDotIndex = e
			},
			/**
			 * 点击banner的处理
			 */
			clickBannerItem(item) {
				// 有外部链接-跳转url
				if (item.open_url) {
					uni.navigateTo({
						url: '/pages/common/webview/webview?url=' + item.open_url + '&title=' + item.title,
						success: res => {},
						fail: () => {},
						complete: () => {}
					});
				}
				// 其余业务处理
			},
			/**
			 * banner数据过滤
			 */
			bannerFormate(bannerList, loading) {
				if (loading) return [];
				if (bannerList.length > 0) return bannerList;
				// 无数据添加默认值
				let list = [{
					"_id": -1,
					"bannerfile": "/static/grid/empty.png",
					"open_url": "https://www.dcloud.io/",
					"title": "内容 A",
				}]
				return list;
			}
		}
	}
</script>

<style>
	/* #ifndef APP-NVUE */
	page {
		display: flex;
		flex-direction: column;
		box-sizing: border-box;
		background-color: #fff;
		min-height: 100%;
		height: auto;
	}
	view {
		font-size: 14px;
		line-height: inherit;
	}
	.example-body {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		flex-wrap: wrap;
		justify-content: center;
		padding: 0;
		font-size: 14px;
		background-color: #ffffff;
	}
	/* #endif */

	/* #ifdef APP-NVUE */
	.warp {
		background-color: #fff;
	}
	/* #endif */

	.example-body {
		flex-direction: column;
		padding: 15px;
		background-color: #ffffff;
	}

	.image {
		width: 50rpx;
		height: 50rpx;
	}

	.text {
		font-size: 26rpx;
		margin-top: 10rpx;
	}

	.example-body {
		/* #ifndef APP-NVUE */
		display: block;
		/* #endif */
	}

	.grid-item-box {
		flex: 1;
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 15px 0;
	}

	.swiper-image {
		width: 750rpx;
		height: 400rpx;
	}

	.swiper-box {
		height: 400rpx;
	}

	.search-icons {
		padding: 16rpx;
	}

	.search-container-bar {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		justify-content: center;
		align-items: center;
		position: fixed;
		left: 0;
		right: 0;
		z-index: 10;
		background-color: #fff;
	}

	/* #ifndef APP-NVUE || VUE3*/
	/deep/
	/* #endif */
	.uni-searchbar__box {
		border-width: 0;
	}

	/* #ifndef APP-NVUE || VUE3 */
	/deep/
	/* #endif */
	.uni-input-placeholder {
		font-size: 28rpx;
	}
</style>
