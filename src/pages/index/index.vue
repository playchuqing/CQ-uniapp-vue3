<template>
	<view class="container">
		<!-- 基础使用 -->
		<view class="_font-14 _b title">基础使用</view>
		<view class="_mar-top-10 _flex-row-mid _flex-wrap" style="row-gap: 20rpx;">
			<view class="btn-container">
				<button class="success-btn" @click="showSuccessDialog = true">成功弹窗</button>
			</view>
			<view class="btn-container">
				<button class="warn-btn" @click="showWarnDialog = true">警示弹窗</button>
			</view>
			<view class="btn-container">
				<button class="error-btn" @click="showErrorDialog = true">失败弹窗</button>
			</view>
		</view>

		<!-- 进阶用法 -->
		<view class="_font-14 _b title _mar-top-10">进阶用法</view>
		<view class="_mar-top-10 _flex-row-mid _flex-wrap" style="row-gap: 20rpx;">
			<view class="btn-container">
				<button class="message-btn" @click="showMessageDialog = true">内容弹窗</button>
			</view>
			<view class="btn-container">
				<button class="animation-btn" @click="showAnimationDialog = true">弹出动画</button>
			</view>
			<view class="btn-container">
				<button class="callback-btn" @click="showCallBackDialog = true">回调弹窗</button>
			</view>
		</view>

		<!-- 扩展用法（文档补充） -->
		<view class="_font-14 _b title _mar-top-10">扩展用法</view>
		<view class="_mar-top-10 _flex-row-mid _flex-wrap" style="row-gap: 20rpx;">
			<view class="btn-container">
				<button class="custom-style-btn" @click="showCustomStyleDialog = true">自定义按钮样式</button>
			</view>
			<view class="btn-container">
				<button class="slot-content-btn" @click="showSlotContentDialog = true">插槽自定义内容</button>
			</view>
			<view class="btn-container">
				<button class="slot-icon-btn" @click="showSlotIconDialog = true">自定义图标</button>
			</view>
			<view class="btn-container">
				<button class="position-btn" @click="showPositionDialog = true">调整位置</button>
			</view>
			<view class="btn-container">
				<button class="confirm-only-btn" @click="showConfirmOnlyDialog = true">仅确认按钮</button>
			</view>
			<view class="btn-container">
				<button class="complex-btn" @click="showComplexDialog = true">综合示例</button>
			</view>
		</view>

		<!-- 弹窗组件实例 -->
		<!-- 基础弹窗 -->
		<CqDialog v-model="showSuccessDialog" icon="success" title="操作成功!"></CqDialog>
		<CqDialog v-model="showWarnDialog" icon="warn" title="警告!"></CqDialog>
		<CqDialog 
			v-model="showErrorDialog" 
			icon="error" 
			title="操作失败!" 
			:btnConfig="{confirmText:'那很可惜了'}"
		></CqDialog>

		<!-- 进阶弹窗 -->
		<CqDialog 
			v-model="showMessageDialog" 
			title="你好呀!" 
			message="很高兴见到你" 
			:mask="false" 
			:btnConfig="{cancelBtn:true}"
		></CqDialog>
		<CqDialog 
			v-model="showAnimationDialog" 
			icon="success" 
			title="动画过渡弹窗" 
			:showAnimation="true"
		></CqDialog>
		<CqDialog 
			v-model="showCallBackDialog" 
			icon="success" 
			title="确认回调" 
			@confirm="successCallBack" 
			@cancel="cancelCallBack" 
			:btnConfig="{cancelBtn:true}"
		></CqDialog>

		<!-- 扩展弹窗（文档补充） -->
		<!-- 1. 自定义按钮样式 -->
		<CqDialog 
			v-model="showCustomStyleDialog" 
			title="自定义按钮样式" 
			message="通过btnStyle配置按钮样式"
			:btnConfig="{
				cancelBtn: true,
				cancelText: '取消',
				confirmText: '确认',
				btnStyle: [
					{ backgroundColor: '#f0f0f0', color: '#333' },
					{ backgroundColor: '#ff4d4f', color: '#fff' }
				]
			}"
		></CqDialog>

		<!-- 2. 插槽自定义内容 -->
		<CqDialog 
			v-model="showSlotContentDialog" 
			title="用户信息"
			:btnConfig="{cancelBtn: true}"
		>
			<template #message>
				<view class="custom-content">
					<view class="info-item">姓名：张三</view>
					<view class="info-item">年龄：25</view>
					<view class="info-item">职业：前端开发</view>
				</view>
			</template>
		</CqDialog>

		<!-- 3. 自定义图标 -->
		<CqDialog 
			v-model="showSlotIconDialog" 
			title="自定义图标示例"
			message="使用slot替换默认图标"
		>
			<template #icon>
				<view class="custom-icon">
					<text style="font-size: 60rpx;">🎉</text>
				</view>
			</template>
		</CqDialog>

		<!-- 4. 调整弹窗位置 -->
		<CqDialog 
			v-model="showPositionDialog" 
			title="调整顶部距离" 
			message="通过top属性设置距离顶部50%"
			top="50"
			:showAnimation="true"
			:btnConfig="{cancelBtn: true}"
		></CqDialog>

		<!-- 5. 仅确认按钮 -->
		<CqDialog 
			v-model="showConfirmOnlyDialog" 
			icon="warn"
			title="提示" 
			message="此弹窗仅显示确认按钮"
			:btnConfig="{confirmText: '我知道了'}"
		></CqDialog>

		<!-- 6. 综合示例 -->
		<CqDialog 
			v-model="showComplexDialog" 
			title="综合配置示例"
			message="包含动画、自定义按钮、回调的综合示例"
			:showAnimation="true"
			:mask="true"
			top="20"
			:btnConfig="{
				cancelBtn: true,
				cancelText: '放弃',
				confirmText: '继续',
				btnStyle: [
					{ backgroundColor: '#6b7280', color: '#fff' },
					{ backgroundColor: '#3b82f6', color: '#fff' }
				]
			}"
			@confirm="handleComplexConfirm"
			@cancel="handleComplexCancel"
		></CqDialog>
	</view>
</template>

<script setup>
import { ref } from 'vue'
import CqDialog from '@/components/CqDialog/dialog.vue'

// 基础弹窗控制变量
const showSuccessDialog = ref(false)
const showWarnDialog = ref(false)
const showErrorDialog = ref(false)

// 进阶弹窗控制变量
const showMessageDialog = ref(false)
const showAnimationDialog = ref(false)
const showCallBackDialog = ref(false)

// 扩展弹窗控制变量
const showCustomStyleDialog = ref(false)
const showSlotContentDialog = ref(false)
const showSlotIconDialog = ref(false)
const showPositionDialog = ref(false)
const showConfirmOnlyDialog = ref(false)
const showComplexDialog = ref(false)

// 原有回调函数
const successCallBack = () => {
  uni.showToast({ title: '你点击了确认按钮', icon: 'none' })
}

const cancelCallBack = () => {
  uni.showToast({ title: '你点击了关闭按钮', icon: 'none' })
}

// 扩展回调函数
const handleComplexConfirm = () => {
  uni.showToast({ title: '点击了继续', icon: 'none' })
}

const handleComplexCancel = () => {
  uni.showToast({ title: '点击了放弃', icon: 'none' })
}
</script>


<style scoped lang="scss">
	// 原有样式保持不变
	.container {
		width: 100%;
		padding: 30rpx;
	}
	.title {
		position: relative;
		padding-bottom: 20rpx;
		width: 100%;
		&:before {
			content: '';
			display: block;
			width: 50%;
			position: absolute;
			bottom: 0;
			left: 0;
			border-bottom: 2rpx solid #bbb;
		}
	}
	.btn-container {
		width: 30%;
		height: 68rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		margin-right: auto;
		&:nth-child(3n) {
			margin-right: 0;
		}
		button {
			width: 100%;
			height: 100%;
			border-radius: 10rpx;
			color: #fff;
			font-size: 30rpx;
			display: flex;
			align-items: center;
			justify-content: center;
		}
		// 原有按钮样式...
		.success-btn { background: #2bb908; }
		.warn-btn { background: #ffc635; }
		.error-btn { background: #e5090c; }
		.message-btn {
			background: linear-gradient(135deg, #1e3a8a 0%, #1e40af 50%, #2563eb 100%);
			box-shadow: 0 10px 30px rgba(30, 58, 138, 0.4), inset 0 1px 0 rgba(255, 255, 255, 0.2);
		}
		.animation-btn {
			background: linear-gradient(135deg, #9ca3af 0%, #c084fc 50%, #a78bfa 100%);
			box-shadow: 0 10px 30px rgba(192, 132, 252, 0.4), inset 0 1px 0 rgba(255, 255, 255, 0.3);
		}
		.callback-btn {
			background: linear-gradient(135deg, #ff4500 0%, #ff6347 50%, #ffa500 100%);
			box-shadow: 0 10px 30px rgba(255, 69, 0, 0.4), inset 0 1px 0 rgba(255, 255, 255, 0.2);
		}
		
		// 新增按钮样式
		.custom-style-btn {
			background: linear-gradient(135deg, #6366f1 0%, #8b5cf6 100%);
		}
		.slot-content-btn {
			background: linear-gradient(135deg, #10b981 0%, #34d399 100%);
		}
		.slot-icon-btn {
			background: linear-gradient(135deg, #f59e0b 0%, #fbbf24 100%);
		}
		.position-btn {
			background: linear-gradient(135deg, #ec4899 0%, #f472b6 100%);
		}
		.confirm-only-btn {
			background: linear-gradient(135deg, #64748b 0%, #94a3b8 100%);
		}
		.complex-btn {
			background: linear-gradient(135deg, #0ea5e9 0%, #38bdf8 100%);
		}
	}
	
	// 自定义内容样式
	.custom-content {
		padding: 20rpx 0;
		.info-item {
			line-height: 60rpx;
			font-size: 28rpx;
			color: #666;
		}
	}
	
	// 自定义图标样式
	.custom-icon {
		display: flex;
		justify-content: center;
		padding: 10rpx 0;
	}
</style>