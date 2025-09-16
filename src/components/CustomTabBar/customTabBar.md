# 自定义 TabBar 组件使用说明

## 1. 组件介绍

本组件是基于 Vue3 + TypeScript 开发的自定义 TabBar 组件，适用于 UniApp 项目，提供底部导航功能，支持图标切换、页面跳转及适配安全区域。

## 2. 组件引入

### 2.1 局部引入

在需要使用的页面中直接导入并使用：

```vue
<template>
  <view>
    <!-- 页面内容 -->
    <CustomTabBar :pathPage="currentPage" />
  </view>
</template>

<script setup lang="ts">
import CustomTabBar from '@/components/CustomTabBar.vue'
import { ref } from 'vue'

// 当前页面路径
const currentPage = ref('/pages/index/pet_calendar')
</script>
```

### 2.2 全局注册

在入口文件（如 `main.ts`）中注册，全局可用：

```typescript
import { createApp } from 'vue'
import App from './App.vue'
import CustomTabBar from '@/components/CustomTabBar.vue'

const app = createApp(App)
app.component('CustomTabBar', CustomTabBar)
app.mount('#app')
```

## 3. Props 参数说明

| 参数名 | 类型 | 说明 | 是否必填 | 默认值 |
|--------|------|------|----------|--------|
| pathPage | string | 当前页面路径，用于高亮当前选中项 | 是 | - |

## 4. 组件数据结构

### 4.1 TabBar 项数据结构

组件内部使用的 `TabBarItem` 接口定义如下：

```typescript
interface TabBarItem {
  pagePath: string;    // 页面路径
  iconPath: string;    // 未选中状态图标路径
  selectedIconPath: string;  // 选中状态图标路径
  text: string;        // 选项文本
}
```

### 4.2 示例数据

```typescript
const tabBarList: TabBarItem[] = [
  {
    pagePath: "/pages/index/pet_calendar",
    iconPath: "/static/tabbar/home.png",
    selectedIconPath: "/static/tabbar/home-active.png",
    text: "首页"
  },
  {
    pagePath: "/pages/index/index",
    iconPath: "/static/tabbar/my.png",
    selectedIconPath: "/static/tabbar/my-active.png",
    text: "个人中心"
  }
];
```

## 5. 功能说明

### 5.1 页面跳转

- 点击 TabBar 项会自动跳转到对应页面
- 对于 TabBar 页面使用 `uni.switchTab()` 进行跳转
- 非 TabBar 页面使用 `uni.navigateTo()` 进行跳转
- 跳转失败时会尝试使用 `uni.reLaunch()` 重新启动

### 5.2 样式适配

- 自动适配底部安全区域（如 iPhone 刘海屏底部）
- 提供阴影效果增强视觉层次感
- 选中项与未选中项有明显的样式区分

### 5.3 高度自适应

组件会在挂载时自动计算自身高度，用于解决页面内容被遮挡的问题。

## 6. 自定义修改

### 6.1 修改 TabBar 选项

直接修改组件内部的 `tabBarList` 数组即可自定义 TabBar 选项：

```typescript
// 添加新的Tab项
const tabBarList: TabBarItem[] = [
  // 原有项...
  {
    pagePath: "/pages/index/message",
    iconPath: "/static/tabbar/message.png",
    selectedIconPath: "/static/tabbar/message-active.png",
    text: "消息"
  }
];
```

### 6.2 修改样式

通过修改组件内的 SCSS 样式可以自定义外观：

- 修改 `tabbar` 类调整整体样式
- 修改 `tabbar-li` 类调整选项样式
- 修改 `active` 类调整选中状态样式
- 调整 `font-size`、`color` 等属性修改文字样式

## 7. 注意事项

1. 确保图标路径正确，否则会显示空白
2. 页面路径必须与路由配置一致
3. 如需修改跳转逻辑，可调整 `pageLink` 方法
4. 在使用自定义 TabBar 时，建议关闭 UniApp 原生 TabBar 配置

## 8. 常见问题

**Q: 点击 Tab 项没有反应？**  
A: 检查 `pagePath` 是否正确，确保与路由配置一致。

**Q: 图标不显示？**  
A: 检查 `iconPath` 和 `selectedIconPath` 是否指向正确的图片资源。

**Q: 在某些设备上底部被遮挡？**  
A: 组件已通过 `safe-area-inset-bottom` 适配安全区域，如仍有问题可调整相关样式。