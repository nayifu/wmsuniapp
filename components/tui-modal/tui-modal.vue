<template>
	<view class="tui-modal__container" :class="[show ? 'tui-modal-show' : '']" :style="{zIndex:zIndex}" @touchmove.stop.prevent>
		<view
			class="tui-modal-box"
			:style="{ width: width, padding: padding, borderRadius: radius, backgroundColor: backgroundColor,zIndex:zIndex+1 }"
			:class="[fadeIn || show ? 'tui-modal-normal' : 'tui-modal-scale', show ? 'tui-modal-show' : '']"
		>
			<view v-if="!custom">
				<view class="tui-modal-title" v-if="title">{{ title }}</view>
				<view class="tui-modal-content" :class="[title ? '' : 'tui-mtop']" :style="{ color: color, fontSize: size + 'rpx' }">{{ content }}</view>
				<view class="tui-modalBtn-box" :class="[button.length != 2 ? 'tui-flex-column' : '']">
					<block v-for="(item, index) in button" :key="index">
						<button
							class="tui-modal-btn"
							:class="[
								'tui-' + (item.type || 'primary') + (item.plain ? '-outline' : ''),
								button.length != 2 ? 'tui-btn-width' : '',
								button.length > 2 ? 'tui-mbtm' : '',
								shape == 'circle' ? 'tui-circle-btn' : ''
							]"
							:hover-class="'tui-' + (item.plain ? 'outline' : item.type || 'primary') + '-hover'"
							:data-index="index"
							@tap="handleClick"
						>
							{{ item.text || $t(`page.goodsDetail.confirm`) }}
						</button>
					</block>
				</view>
			</view>
			<view v-else><slot></slot></view>
		</view>
		<view class="tui-modal-mask" :class="[show ? 'tui-mask-show' : '']" :style="{zIndex:maskZIndex}" @tap="handleClickCancel"></view>
	</view>
</template>

<script>
export default {
	name: 'tuiModal',
	emits: ['click','cancel'],
	props: {
		//是否显示
		show: {
			type: Boolean,
			default: false
		},
		width: {
			type: String,
			default: '84%'
		},
		backgroundColor: {
			type: String,
			default: '#fff'
		},
		padding: {
			type: String,
			default: '40rpx 64rpx'
		},
		radius: {
			type: String,
			default: '24rpx'
		},
		//标题
		title: {
			type: String,
			default: ''
		},
		//内容
		content: {
			type: String,
			default: ''
		},
		//内容字体颜色
		color: {
			type: String,
			default: '#999'
		},
		//内容字体大小 rpx
		size: {
			type: Number,
			default: 28
		},
		//形状 circle, square
		shape: {
			type: String,
			default: 'square'
		},
		button: {
			type: Array,
			default: function() {
				return [
					{
						text: this.$t(`page.goodsDetail.cancel`),
						type: 'red',
						plain: true //是否空心
					},
					{
						text: this.$t(`page.goodsDetail.confirm`),
						type: 'red',
						plain: false
					}
				];
			}
		},
		//点击遮罩 是否可关闭
		maskClosable: {
			type: Boolean,
			default: true
		},
		//淡入效果，自定义弹框插入input输入框时传true
		fadeIn: {
			type: Boolean,
			default: false
		},
		//自定义弹窗内容
		custom: {
			type: Boolean,
			default: false
		},
		//容器z-index
		zIndex:{
			type: Number,
			default: 999
		},
		//mask z-index
		maskZIndex:{
			type: Number,
			default: 998
		}
	},
	data() {
		return {};
	},
	methods: {
		handleClick(e) {
			if (!this.show) return;
			const dataset = e.currentTarget.dataset;
			this.$emit('click', {
				index: Number(dataset.index)
			});
		},
		handleClickCancel() {
			if (!this.maskClosable) return;
			this.$emit('cancel');
		}
	}
};
</script>

<style scoped>
.tui-modal__container {
	width: 100%;
	height: 100%;
	position: fixed;
	left: 0;
	top: 0;
	display: flex;
	align-items: center;
	justify-content: center;
	visibility: hidden;
}
.tui-modal-box {
	position: relative;
	opacity: 0;
	visibility: hidden;
	box-sizing: border-box;
	transition: all 0.3s ease-in-out;
}

.tui-modal-scale {
	transform: scale(0);
}

.tui-modal-normal {
	transform: scale(1);
}

.tui-modal-show {
	opacity: 1;
	visibility: visible;
}

.tui-modal-mask {
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background-color: rgba(0, 0, 0, 0.6);
	transition: all 0.3s ease-in-out;
	opacity: 0;
	visibility: hidden;
}

.tui-mask-show {
	visibility: visible;
	opacity: 1;
}

.tui-modal-title {
	text-align: center;
	font-size: 34rpx;
	color: #333;
	padding-top: 20rpx;
	font-weight: bold;
}

.tui-modal-content {
	text-align: center;
	color: #999;
	font-size: 28rpx;
	padding-top: 20rpx;
	padding-bottom: 60rpx;
}

.tui-mtop {
	margin-top: 30rpx;
}

.tui-mbtm {
	margin-bottom: 30rpx;
}

.tui-modalBtn-box {
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: space-between;
}

.tui-flex-column {
	flex-direction: column;
}

.tui-modal-btn {
	width: 46%;
	height: 68rpx;
	line-height: 68rpx;
	position: relative;
	border-radius: 10rpx;
	font-size: 26rpx;
	overflow: visible;
	margin-left: 0;
	margin-right: 0;
}

.tui-modal-btn::after {
	content: ' ';
	position: absolute;
	width: 200%;
	height: 200%;
	-webkit-transform-origin: 0 0;
	transform-origin: 0 0;
	transform: scale(0.5, 0.5) translateZ(0);
	left: 0;
	top: 0;
	border-radius: 20rpx;
	z-index: 2;
}

.tui-btn-width {
	width: 80% !important;
}

.tui-primary {
	background: #5677fc;
	color: #fff;
}

.tui-primary-hover {
	background: #4a67d6;
	color: #e5e5e5;
}

.tui-primary-outline {
	color: #5677fc;
	background: transparent;
}

.tui-primary-outline::after {
	border: 1px solid #5677fc;
}

.tui-danger {
	background: #ed3f14;
	color: #fff;
}

.tui-danger-hover {
	background: #d53912;
	color: #e5e5e5;
}

.tui-danger-outline {
	color: #ed3f14;
	background: transparent;
}

.tui-danger-outline::after {
	border: 1px solid #ed3f14;
}

.tui-red {
	background: #e41f19;
	color: #fff;
}

.tui-red-hover {
	background: #c51a15;
	color: #e5e5e5;
}

.tui-red-outline {
	color: #e41f19;
	background: transparent;
}

/* .tui-red-outline::after {
	border: 1px solid #e41f19;
} */

.tui-warning {
	background: #ff7900;
	color: #fff;
}

.tui-warning-hover {
	background: #e56d00;
	color: #e5e5e5;
}

.tui-warning-outline {
	color: #ff7900;
	background: transparent;
}

.tui-warning-outline::after {
	border: 1px solid #ff7900;
}

.tui-green {
	background: #19be6b;
	color: #fff;
}

.tui-green-hover {
	background: #16ab60;
	color: #e5e5e5;
}

.tui-green-outline {
	color: #19be6b;
	background: transparent;
}

.tui-green-outline::after {
	border: 1px solid #19be6b;
}

.tui-white {
	background: #fff;
	color: #333;
}

.tui-white-hover {
	background: #f7f7f9;
	color: #666;
}

.tui-white-outline {
	color: #333;
	background: transparent;
}

.tui-white-outline::after {
	border: 1px solid #333;
}

.tui-gray {
	background: #ededed;
	color: #999;
}

.tui-gray-hover {
	background: #d5d5d5;
	color: #898989;
}

.tui-gray-outline {
	color: #999;
	background: transparent;
}

.tui-gray-outline::after {
	border: 1px solid #999;
}

.tui-outline-hover {
	opacity: 0.6;
}

.tui-circle-btn {
	border-radius: 40rpx !important;
}

.tui-circle-btn::after {
	border-radius: 80rpx !important;
}
</style>
