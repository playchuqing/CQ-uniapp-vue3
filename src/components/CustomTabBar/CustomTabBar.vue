<template>
	<view class="footer-container">
		<view class="tabbar-hideen" :style="{ height: tabbarHeight + 'px' }"></view>
		<view class="tabbar">
			<view class="tabbar-list">
				<view 
					class="tabbar-li" 
					:class="{'active': pathPage === tabar.pagePath}"
					v-for="(tabar, index) in tabBarList" 
					:key="index" 
					@click="pageLink(tabar.pagePath)"
				>
					<view class="li-icon">
						<image 
							:src="pathPage === tabar.pagePath ? tabar.selectedIconPath : tabar.iconPath"
							mode="aspectFit"
						></image>
					</view>
					<view class="li-name">{{ tabar.text }}</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';

// 定义TabBar项的接口
interface TabBarItem {
  pagePath: string;
  iconPath: string;
  selectedIconPath: string;
  text: string;
}

// 接收父组件传入的当前页面路径
const props = defineProps<{
  pathPage: string;
}>();

// 存储TabBar列表数据
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

// 存储tabbar高度
const tabbarHeight = ref<string>('0');

// 页面跳转方法
const pageLink = (url: string) => {
  if (!url || url === props.pathPage) {
    return;
  }
  
  if (isTabBarPage(url)) {
    uni.switchTab({
      url: url,
      fail: () => {
        uni.reLaunch({
          url: url
        });
      }
    });
  } else {
    uni.navigateTo({
      url: url
    });
  }
};

// 判断是否为TabBar页面
const isTabBarPage = (pathPath: string): boolean => {
  return tabBarList.some(item => item.pagePath === pathPath);
};

// 获取tabbar高度
const getTabbarHeight = () => {
  uni.createSelectorQuery().in(getCurrentInstance()!).select('.tabbar').boundingClientRect((rect) => {
    if (rect) {
      tabbarHeight.value = parseFloat(rect.height).toFixed(2);
    }
  }).exec();
};

// 组件挂载时获取高度
onMounted(() => {
  getTabbarHeight();
});
</script>

<style scoped lang="scss">
	.footer-container {}

	.tabbar {
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		width: 100%;
		box-shadow: 0px 0px 8px 0px rgba(99, 99, 99, 0.1);
		background: #fff;
	}

	.tabbar .tabbar-list {
		width: 100%;
		padding-top: 24rpx;
		padding-bottom: calc(constant(safe-area-inset-bottom));
		padding-bottom: calc(env(safe-area-inset-bottom));
		display: flex;
		flex-direction: row;
	}

	.tabbar .tabbar-list .tabbar-li {
		flex: 1 0;
		display: flex;
		flex-direction: column;
		align-items: center;
		row-gap: 8rpx;
		color: #bfbfbf;
	}

	.tabbar .tabbar-list .tabbar-li.active {
		color: #2c2c2c;
	}

	.tabbar .tabbar-list .tabbar-li .li-icon {
		width: 46rpx;
		height: 46rpx;
	}

	.tabbar .tabbar-list .tabbar-li .li-icon image {
		width: 100%;
		height: 100%;
	}

	.tabbar .tabbar-list .tabbar-li .li-name {
		font-size: 26rpx;
	}
</style>