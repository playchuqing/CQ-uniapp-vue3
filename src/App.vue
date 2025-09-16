<script setup lang="ts">
import { onLaunch, onShow, onHide } from "@dcloudio/uni-app";
onLaunch(() => {
  console.log("App Launch");
});
onShow(() => {
  console.log("App Show");
});
onHide(() => {
  console.log("App Hide");
});
</script>
<style lang="scss">
	@import 'static/css/base.css';
	@import 'static/css/guildford.css';
	@import 'static/css/style.scss';// Sass 即将在 3.0.0 版本中移除 @import 语法，推荐使用新的 @use 和 @forward 语法替代。
	@import 'static/iconfont_cq/iconfont.css';
	page {
		background: #f8f8f8;
	}
</style>

<style>
	/* #ifdef MP-WEIXIN */
	@import 'static/css/mySass/app.wxss';
	/* #endif */
	
</style>
