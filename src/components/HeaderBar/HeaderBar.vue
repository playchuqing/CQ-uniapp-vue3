<script setup lang="ts">
import { ref, onMounted, watch, computed } from 'vue';
import { useStore } from 'vuex'; // 如需使用可保留，否则可删除

// 1. 定义类型
type Config = {
  indexBgHid: boolean;
  backgroundColor: string;
  color: string;
  bgUrl: string;
};

type HeaderBarProps = {
  Config?: Partial<Config>;
  homePath?: string;
  scrollTop?: number;
  BarTitle?: string;
  showbgImg?: boolean;
  OpacityScroll?: boolean;
  Back?: boolean;
};

// 2. 接收Props - 将默认配置直接写在withDefaults中
const props = withDefaults(defineProps<HeaderBarProps>(), {
  // 直接在这里定义默认配置，不引用外部变量
  Config: () => ({
    indexBgHid: false,
    backgroundColor: '#fff',
    color: '#333333',
    bgUrl: ''
  }),
  homePath: '/pages/index/home',
  scrollTop: 0,
  BarTitle: 'navigationBar',
  showbgImg: false,
  OpacityScroll: false,
  Back: true
});

// 组件状态
const recHeight = ref<number>(0); // 状态栏高度
const titleBarHeight = ref<number>(0); // 顶部标题栏高度
const MenuButtonHeight = ref<number>(0); // 小胶囊高度（小程序）
const MenuButtonTop = ref<number>(0); // 小胶囊顶部距离（小程序）
const headerHeight = ref<number>(88); // 头部总高度
const windowTop = ref<number>(88); // H5头部高度
const opacity = ref<number>(0);
const page_count = ref<number>(1);

// 初始化
onMounted(() => {
  if (!props.OpacityScroll) {
    opacity.value = 1;
  }
  uni.hideTabBar();
  
  // 小程序环境获取胶囊信息
  // #ifdef MP-WEIXIN || MP
  const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
  MenuButtonHeight.value = menuButtonInfo.height;
  MenuButtonTop.value = menuButtonInfo.top;
  // #endif
  
  getPhoneInfo();
  const pages = getCurrentPages();
  page_count.value = pages.length;
});

// 监听滚动高度变化
watch(
  () => props.scrollTop,
  (val) => {
    if (props.OpacityScroll) {
      opacity.value = Number(val) >= 1 ? 1 : Number(val);
    }
  },
  { immediate: true }
);

// 获取设备信息
const getPhoneInfo = () => {
  uni.getSystemInfo({
    success(res) {
      recHeight.value = Number(res.statusBarHeight) || 0;
      
      // #ifdef MP-WEIXIN || MP
      titleBarHeight.value = MenuButtonHeight.value + (MenuButtonTop.value - recHeight.value) * 2;
      headerHeight.value = recHeight.value + titleBarHeight.value;
      // #endif
      
      // #ifdef H5 || APP
      windowTop.value = Number(res.windowTop) || 88;
      titleBarHeight.value = 44;
      // #endif
    }
  });
};

// 返回操作
const goBack = () => {
  if (page_count.value === 1) {
    uni.switchTab({
      url: props.homePath,
      fail() {
        uni.reLaunch({
          url: props.homePath
        });
      }
    });
  } else {
    uni.navigateBack();
  }
};

// 计算属性 - 处理配置合并
const mergedConfig = computed<Config>(() => ({
  // 这里使用默认配置的字面量，不引用外部变量
  indexBgHid: false,
  backgroundColor: '#fff',
  color: '#333333',
  bgUrl: '',
  ...props.Config
}));
</script>

<template>
  <view class="nav-bar-header">
    <!-- 自定义头部 -->
    <view class="header_nav">
      <!-- 状态栏：用 recHeight + 'px' 拼接单位（recHeight 为数字类型） -->
      <view class="status_bar" :style="{ height: recHeight + 'px' }"></view>
      
      <view class="header_title"
            :style="{ 
              height: titleBarHeight + 'px', 
              top: recHeight + 'px', // 修复：数字转字符串拼接单位
              lineHeight: titleBarHeight + 'px' 
            }">
        <slot>
          <view v-if="Back" 
                class="icon"
                :class="{
                  'iconfont-cq cq-xiaochengxufanhui _pad-right-15 _left': page_count > 1,
                  'iconfont-cq cq-xiaochengxufanhuishouyeanniu _b _home': page_count <= 1
                }" 
                :style="{ 
                  color: mergedConfig.color,
                  '--home-height': titleBarHeight + 'px'
                }"
                style="font-size: 36rpx; position: absolute; z-index: 99;"
                @click="goBack">
          </view>
          
          <view :style="{ color: mergedConfig.color }"
                style="font-size: 36rpx; position: relative; display: flex; align-items: center; justify-content: center;">
            {{ BarTitle }}
          </view>
        </slot>
      </view>

      <!-- H5/APP 背景图：修复 recHeight 运算（数字直接相加，再拼接单位） -->
      <!-- #ifdef H5 || APP -->
      <view class="img"
            :style="{
              height: (titleBarHeight + recHeight) + 'px', // 修复：无需 parseInt（recHeight 已为数字）
              opacity: opacity,
              background: mergedConfig.backgroundColor
            }">
        <image v-if="showbgImg && !OpacityScroll" 
               style="width: 100%;" 
               :src="mergedConfig.bgUrl" 
               mode="widthFix">
        </image>
      </view>
      <!-- #endif -->

      <!-- 微信小程序背景图 -->
      <!-- #ifdef MP-WEIXIN -->
      <view class="img"
            :style="{
              height: headerHeight + 'px',
              opacity: opacity,
              background: mergedConfig.backgroundColor
            }">
        <image v-if="showbgImg && !OpacityScroll" 
               style="width: 100%;" 
               :src="mergedConfig.bgUrl" 
               mode="widthFix">
        </image>
      </view>
      <!-- #endif -->
    </view>

    <!-- H5/APP 索引背景：修复 recHeight 运算 -->
    <!-- #ifdef H5 || APP -->
    <view class="index-bg" 
          :class="{'index-bg-hid': mergedConfig.indexBgHid}"
          :style="{ height: (titleBarHeight + recHeight) + 'px' }"> <!-- 修复：数字相加后拼接单位 -->
      <image v-if="showbgImg" 
             style="width: 100%;" 
             :src="mergedConfig.bgUrl" 
             mode="widthFix">
      </image>
    </view>
    <!-- #endif -->

    <!-- 微信小程序索引背景 -->
    <!-- #ifdef MP-WEIXIN -->
    <view class="index-bg" 
          :class="{'index-bg-hid': mergedConfig.indexBgHid}"
          :style="{ height: headerHeight + 'px' }">
      <image v-if="showbgImg" 
             style="width: 100%;" 
             :src="mergedConfig.bgUrl" 
             mode="widthFix">
      </image>
    </view>
    <!-- #endif -->
  </view>
</template>

<style scoped lang="scss">
.nav-bar-header {
  width: 100%;
}

.header_nav {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 999;
}

.header_nav .status_bar {
  width: 100%;
  position: fixed;
}

.header_nav .header_title {
  width: 100%;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 99;
}

.header_nav .img {
  position: absolute;
  top: 0;
  z-index: 10;
  width: 100%;
  overflow: hidden;
}

.index-bg {
  width: 100%;
}

.index-bg-hid {
  overflow: hidden;
}

.icon {
  border-radius: 50%;
  font-size: 24rpx;
  left: 30rpx;
  width: calc(var(--home-height) - 8px);
  height: calc(var(--home-height) - 8px);
  display: flex;
  align-items: center;
  justify-content: center;
}

._home {
  background: rgba(0, 0, 0, 0.1);
}
</style>