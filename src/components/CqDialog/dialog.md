# cq-dialog 组件文档

作者：初晴  
邮箱：play168chuqing@168.com

## 简介
`cq-dialog` 是一个基于 Vue3 的弹窗组件，支持自定义标题、消息内容、图标、按钮样式等，包含显示/隐藏动画效果，通过 `v-model` 实现双向绑定控制显示状态，可用于展示提示信息、确认操作等场景。

## 组件属性（Props）

| 属性名 | 类型 | 默认值 | 说明 |
|--------|------|--------|------|
| modelValue | Boolean | false | 控制弹窗显示/隐藏的状态（支持 `v-model` 双向绑定） |
| icon | String | 'success' | 弹窗图标类型，可选值：'success'（成功）、'warn'（警告）、'error'（错误） |
| title | String | '提示' | 弹窗标题文本 |
| message | String | null | 弹窗消息内容（若使用message插槽，此属性无效） |
| mask | Boolean | true | 是否显示遮罩层 |
| showAnimation | Boolean | false | 是否启用显示/隐藏动画 |
| top | String | null | 弹窗距离页面顶部的百分比（如：'30' 表示30%） |
| btnConfig | Object | 见下文 | 按钮配置对象，包含按钮显示状态、文本和样式 |

### btnConfig 默认值
```javascript
{
  cancelBtn: false, // 是否显示取消按钮
  cancelText: '关闭', // 取消按钮文本
  confirmText: '好的', // 确认按钮文本
  btnStyle: [{}, {}] // 按钮样式，数组第一项为取消按钮样式，第二项为确认按钮样式
}
```

### btnStyle 特殊处理
组件对 `btnStyle` 做了增强处理：
- 若传入单个数组元素（如 `[{}]`），默认作为确认按钮样式
- 若传入对象（非数组），自动转换为 `[{}, 传入对象]`，作为确认按钮样式
- 确保最终始终是长度为2的数组（不足时自动补默认空对象）

## 插槽（Slots）

| 插槽名 | 说明 |
|--------|------|
| icon | 自定义图标区域，会覆盖默认图标 |
| message | 自定义消息内容区域，会覆盖message属性 |

## 事件（Events）

| 事件名 | 说明 | 回调参数 |
|--------|------|----------|
| confirm | 点击确认按钮时触发 | 无 |
| cancel | 点击取消按钮时触发 | 无 |
| update:modelValue | 弹窗关闭时触发，用于 `v-model` 双向绑定 | false（关闭状态） |

## 样式说明

### 图标样式
- 成功图标（icon="success"）：绿色（#2bb908），大小82rpx
- 警告图标（icon="warn"）：黄色（#ffc635），大小82rpx
- 错误图标（icon="error"）：红色（#e5090c），大小82rpx

### 按钮样式
- 确认按钮：默认绿色背景（#2bb908），白色文字，圆角200rpx
- 取消按钮：默认白色背景，灰色文字（#999），灰色边框，圆角200rpx
- 可通过`btnConfig.btnStyle`自定义按钮样式

### 动画效果
当`showAnimation`为true时启用：
- 显示动画：遮罩层淡入（0→0.4透明度），弹窗从缩放0到1
- 隐藏动画：遮罩层淡出（0.4→0透明度），弹窗从缩放1到0
- 动画时长通过组件内部`duration`控制（默认0.3秒）

## 使用示例

### 基础用法（v-model绑定）
```vue
<template>
  <cq-dialog 
    v-model="showDialog" 
    title="操作提示" 
    message="确定要执行此操作吗？" 
    @confirm="handleConfirm"
    @cancel="handleCancel"
  ></cq-dialog>
</template>

<script setup>
import { ref } from 'vue'
import cqDialog from './dialog.vue'

const showDialog = ref(false)

const handleConfirm = () => {
  console.log('用户确认操作')
}

const handleCancel = () => {
  console.log('用户取消操作')
}
</script>
```

### 显示取消按钮
```vue
<cq-dialog 
  v-model="showDialog" 
  title="提示" 
  message="这是一个带取消按钮的弹窗" 
  :btnConfig="{ cancelBtn: true, cancelText: '取消', confirmText: '确定' }"
></cq-dialog>
```

### 自定义按钮样式
```vue
<cq-dialog 
  v-model="showDialog" 
  title="样式自定义" 
  message="这是自定义按钮样式的弹窗" 
  :btnConfig="{ 
    cancelBtn: true,
    btnStyle: [
      { backgroundColor: '#f5f5f5', color: '#666' }, // 取消按钮样式
      { backgroundColor: '#1890ff', color: '#fff' }  // 确认按钮样式
    ]
  }"
></cq-dialog>
```

### 使用插槽自定义内容
```vue
<cq-dialog 
  v-model="showDialog" 
  title="自定义内容"
>
  <template #icon>
    <view class="custom-icon">★</view>
  </template>
  <template #message>
    <view class="custom-message">
      <text>这是通过插槽自定义的消息内容</text>
      <view style="margin-top: 10rpx;">可以包含多行文本或其他元素</view>
    </view>
  </template>
</cq-dialog>
```

### 启用动画效果
```vue
<cq-dialog 
  v-model="showDialog" 
  title="带动画的弹窗" 
  message="这是一个启用了显示/隐藏动画的弹窗" 
  showAnimation
></cq-dialog>
```