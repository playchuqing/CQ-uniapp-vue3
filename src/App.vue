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
	@import 'static/css/style.scss';
	@import 'static/iconfont_cq/iconfont.css';
	page {
		background: #f8f8f8;
	}
</style>

<style>
	@import 'static/css/mySass/app.wxss';
</style>
