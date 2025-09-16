<template>
	<view class="dialog-container" :class="showAnimation ? animation : 'dialog-default'"
			:style="`--dialog-duration: ${duration}s`" v-if="showAnimation ? dialog : modelValue">
		<view class="dialog-mask" v-if="mask"></view>
		<view class="dialog-main" :style="{ top: top ? `${top}%` : 'auto' }">
			<slot name="icon">
				<view class="_mar-bottom-10">
					<text class="iconfont-cq" :class="{
							'cq-chenggong': icon === 'success',
							'cq-tixingshixin': icon === 'warn',
							'cq-shibai': icon === 'error'
						}">
					</text>
				</view>
			</slot>
			<view class="title">{{ title }}</view>
			<view class="message" v-if="message">
				{{ message }}
			</view>
			<slot name="message"></slot>
			<view class="action">
				<view class="cancel-btn" :style="[mergedBtnConfig.btnStyle[0]]" v-if="mergedBtnConfig.cancelBtn" @click="cancel">
					{{ mergedBtnConfig.cancelText }}
				</view>
				<view class="confirm-btn" :style="[mergedBtnConfig.btnStyle[1]]" @click="confirm">
					{{ mergedBtnConfig.confirmText }}
				</view>
			</view>
		</view>
	</view>
</template>

<script setup>
import { ref, watch, computed, onMounted } from 'vue'

// 定义 props
const props = defineProps({
	// 基础配置
	modelValue: {
		type: Boolean,
		default: false
	},
	icon: {
		type: String,
		default: 'success'
	},
	title: {
		type: String,
		default: '提示'
	},
	message: {
		type: String,
		default: null
	},
	mask: {
		type: Boolean,
		default: true
	},
	showAnimation: {
		type: Boolean,
		default: false
	},
	// 距离页面顶部百分比
	top: {
		type: String,
		default: null
	},
	// 按钮配置
	btnConfig: {
		type: Object,
		default: () => ({
			cancelBtn: false,
			cancelText: '关闭',
			confirmText: '好的',
			btnStyle: [{}, {}]
		})
	}
})

// 定义 emits
const emit = defineEmits(['update:modelValue', 'confirm', 'cancel'])

// 组件内部状态
const animation = ref('')
const duration = ref(0.3)
const dialog = ref(false)

// 监听 modal 变化处理动画逻辑
watch(
	() => props.modelValue,
	(newVal) => {
		const s = duration.value * 1000
		if (newVal) {
			animation.value = 'dialog__start'
			dialog.value = true
		} else {
			animation.value = 'dialog__end'
			setTimeout(() => {
				dialog.value = false
			}, s)
		}
	},
	{ immediate: true }
)

// 合并按钮配置
const mergedBtnConfig = computed(() => {
	// 获取默认配置
	const defaultBtnConfig = {
		cancelBtn: false,
		cancelText: '关闭',
		confirmText: '好的',
		btnStyle: [{}, {}]
	}
	// 合并配置
	const merged = { ...defaultBtnConfig, ...props.btnConfig }

	// 处理按钮样式
	if (merged.btnStyle) {
		if (Array.isArray(merged.btnStyle)) {
			if (merged.btnStyle.length === 1) {
				merged.btnStyle = [{}, merged.btnStyle[0]]
			} else if (merged.btnStyle.length < 2) {
				merged.btnStyle = merged.btnStyle.concat(Array(2 - merged.btnStyle.length).fill({}))
			}
		} else if (typeof merged.btnStyle === 'object') {
			merged.btnStyle = [{}, merged.btnStyle]
		}
	}
	return merged
})

// 方法定义
const close = () => {
	emit('update:modelValue', false)
}

const confirm = () => {
	emit('confirm')
	close()
}

const cancel = () => {
	emit('cancel')
	close()
}

onMounted(() => {
	// 可添加初始化逻辑
})
</script>

<style scoped lang="scss">
/* 样式部分与原代码保持一致 */
.dialog-container {
	position: fixed;
	bottom: 0;
	left: 0;
	right: 0;
	top: 0;
	z-index: 999;
	width: 100%;
	height: 100vh;
	display: flex;
	align-items: center;
	justify-content: center;
}

.dialog-container .dialog-mask {
	position: relative;
	width: 100%;
	height: 100%;
}

.dialog-container .dialog-main {
	position: absolute;
	width: 500rpx;
	background-color: #fff;
	border-radius: 28rpx;
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 50rpx 40rpx;
	transform-origin: center center;
	transform: scale(0);
}

.cq-chenggong {
	color: #2bb908;
	font-size: 82rpx;
}

.cq-tixingshixin {
	color: #ffc635;
	font-size: 82rpx;
}

.cq-shibai {
	color: #e5090c;
	font-size: 82rpx;
}

.dialog-container .dialog-main .title {
	color: #333;
	font-size: 36rpx;
	font-weight: bold;
}

.dialog-container .dialog-main .message {
	margin-top: 20rpx;
	color: #999;
	font-size: 26rpx;
	text-align: center;
}

.dialog-container .dialog-main .action {
	margin-top: 30rpx;
	display: flex;
	flex-direction: row;
	justify-content: center;
	column-gap: 20rpx;
	width: 100%;
}

.dialog-container .dialog-main .action .cancel-btn,
.dialog-container .dialog-main .action .confirm-btn {
	flex: 0 0 50%;
	line-height: 68rpx;
	text-align: center;
	background-color: #2bb908;
	color: #fff;
	font-size: 28rpx;
	border-radius: 200rpx;
	height: 68rpx;
}

.dialog-container .dialog-main .action .cancel-btn {
	color: #999;
	background: #fff;
	border: 1px solid rgba(153, 153, 153, 1);
}

.dialog-default .dialog-mask {
	background-color: rgba(0, 0, 0, .4);
}

.dialog-default .dialog-main {
	transform: scale(1);
}

.dialog__start {
	.dialog-mask {
		animation-name: fade_start;
		animation-duration: var(--dialog-duration);
		animation-timing-function: ease-out;
		animation-fill-mode: forwards;
	}

	.dialog-main {
		animation-name: fadeInScale;
		animation-duration: var(--dialog-duration);
		animation-fill-mode: forwards;
		animation-timing-function: ease;
	}
}

.dialog__end {
	.dialog-mask {
		background-color: rgba(0, 0, 0, .4);
		animation-name: fade_end;
		animation-duration: var(--dialog-duration);
		animation-timing-function: ease-out;
		animation-fill-mode: forwards;
	}

	.dialog-main {
		transform: scale(1);
		animation-name: fadeOutScale;
		animation-duration: var(--dialog-duration);
		animation-fill-mode: forwards;
		animation-timing-function: ease;
	}
}

@keyframes fade_start {
	from {
		background-color: rgba(0, 0, 0, 0);
	}

	to {
		background-color: rgba(0, 0, 0, .4);
	}
}

@keyframes fade_end {
	from {
		background-color: rgba(0, 0, 0, 0.4);
	}

	to {
		background-color: rgba(0, 0, 0, 0);
	}
}

@keyframes fadeInScale {
	from {
		transform: scale(0);
	}

	to {
		transform: scale(1);
	}
}

@keyframes fadeOutScale {
	from {
		transform: scale(1);
	}

	to {
		transform: scale(0);
	}
}
</style>