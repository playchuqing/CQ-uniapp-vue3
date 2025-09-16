# UniApp 小程序与 H5 前端开发规范指南
## 一、视图规范
### （一）id/class 命名规则
1. **基于内容命名优先**：按照页面元素的实际内容命名，例如页面头部命名为`header`、主体内容区为`content`、底部为`footer`。
2. **结合行为表现辅助命名**：当依据内容难以精准命名时，参考元素行为特性辅助命名，如占据主要列的区域命名为`col-main`、蓝色背景盒子为`blue-box`。
3. **命名格式要求**：全小写形式，多单词用`-`连接（禁止下划线、驼峰命名）；避免与`text`、`image`等基础标签重名；子元素可加父元素前缀（如`main-title`、`main-text`）。
4. **合理使用缩写**：不影响语义理解时，对结构类词汇使用常见缩写（如`col`=column、`nav`=navigation、`btn`=button），禁止编造不常见缩写。
5. **规避广告拦截词汇**：命名中禁止出现`ad`、`ads`、`advbanner`、`sponsor`等广告相关词汇，防止元素被广告拦截工具误判。

### （二）语法规则
1. **元素缩进**：嵌套元素缩进 1 次（HbuilderX 默认 4 个空格，可团队统一为 2 个空格），保证代码层级清晰。
2. **属性引号**：所有属性定义统一使用双引号，例如`class="container"`、`v-if="isShow"`、`@click="handleClick()"`，禁止使用单引号。
3. **自闭合元素斜线**：UniApp 中自闭合元素（如`<view />`、`<image />`）尾部斜线可加可不加，团队统一即可。
4. **结束标签不可省略**：非自闭合标签（如`<view>`、`<text>`）必须完整书写结束标签，删除时需成对删除，避免标签遗漏导致布局错乱。
5. **点击事件括号**：`@click`绑定的方法必须加括号，如`@click="preview()"`，确保方法能正确触发（无参数时也需保留括号，保持格式统一）。
6. **图标选择**：优先使用项目已有的`icon`图标（如 Uview 组件库的`u-icon`），避免使用图片图标，减少资源加载量、提升开发效率。
7. **条件判断优化**：View 中存在多个条件判断时，将逻辑提取到`computed`计算属性中，处理后返回结果再在视图中使用，提升代码可读性与可维护性。  
   示例：
   ```javascript
   computed: {
     // 提取多条件判断逻辑
     showSubmitBtn() {
       return this.form.name && this.form.phone && !this.isSubmitting;
     }
   }
   ```
   ```html
   <!-- 视图中直接使用计算属性，无需重复写判断 -->
   <button @click="submit()" v-if="showSubmitBtn">提交</button>
   ```
8. **图片宽高设置**：需分场景适配，兼顾视觉稳定性与布局灵活性：
   - 若为固定比例图（如商品卡、头像）：优先用“父容器定宽+`padding-top`控比例+图片绝对定位”，避免首次加载拉伸，示例：
     ```html
     <!-- 父容器定宽+1:1比例 -->
     <view class="img-box">
       <image class="img" :src="imgUrl" mode="aspectFill"></image>
     </view>
     <style>
     .img-box {
       width: 200rpx; /* 可设width:100%撑满外层父盒 */
       position: relative;
       overflow: hidden; /* 配合aspectFill裁剪超出部分 */
       padding-top: 100%; /* 1:1比例，4:3图设75%，16:9图设56.25% */
     }
     .img {
       position: absolute;
       top: 0; left: 0;
       width: 100%; height: 100%; /* 填充父容器，避免拉伸 */
     }
     </style>
     ```
   - 若需父盒撑满且高度自适应（如富文本插图）：可使用`widthfix`，但需加骨架屏/默认背景占位，防止加载跳动，示例：
     ```html
     <!-- 父盒撑满+widthfix自适应，搭配骨架屏 -->
     <view class="rich-img-wrap">
       <!-- 加载中：骨架屏占位（避免页面跳动） -->
       <view class="img-skeleton" v-if="!isLoaded"></view>
       <!-- 加载完成：widthfix自适应高度 -->
       <image 
         v-else 
         class="rich-img" 
         :src="imgUrl" 
         mode="widthfix"
         @load="isLoaded = true" <!-- 图片加载完成后隐藏骨架屏 -->
       ></image>
     </view>
     <style>
     .rich-img-wrap { width: 100%; } /* 父盒撑满外层容器 */
     .img-skeleton {
       width: 100%;
       height: 300rpx; /* 预设占位高度，匹配常见图片比例 */
       background: #f5f5f5; /* 灰色背景模拟加载中状态 */
     }
     .rich-img { width: 100%; } /* widthfix需配合width:100%生效 */
     </style>
     ```
9. **v-for 与 key 配对**：使用`v-for`进行列表渲染时，必须设置`key`值（优先用后端返回的唯一 ID，如`item.id`，避免用`index`），帮助 Vue 高效更新虚拟 DOM，提升渲染性能。  
   示例：`<view v-for="(item, index) in list" :key="item.id">{{ item.name }}</view>`。

### （三）属性顺序
按“核心属性 → Vue 指令 → 事件属性 → 其他属性”的顺序排列，保证代码可读性：
1. **核心属性优先**：`class`（第一位，用于组件复用）、`id`、`name`、`style`（尽量少用行内样式）。
2. **Vue 指令其次**：`v-for`、`v-if`、`v-show`、`v-model`等。
3. **事件属性随后**：`@click`、`@change`、`@input`、`@load`等。
4. **其他属性最后**：UniApp 特定属性（如`src`、`value`、`placeholder`）、自定义属性；若有插槽（Slot），插槽内容放在元素最后。

> 注：布尔型属性（如`disabled`、`readonly`）声明时可不赋值，直接写`<input disabled>`；行内样式`style`会增加维护成本，优先用类名控制样式。

### （四）减少标签数量
避免多余父元素，减少浏览器解析与渲染压力：
1. 内部元素的宽高不可超过外层`view`，防止布局混乱。
2. 实现布局时，优先用 CSS 样式（如`flex`、`margin`）替代空标签，例如列表详情页无左侧语音条时，用`padding-left`调整布局，而非添加空`<view>`。

### （五）注释规范
关键代码块、复杂逻辑、接口调用处必须加注释，描述“功能+参数+返回值”（简洁明了，避免冗余）：
- **HTML 注释**：标注模块功能，如`<!-- 商品列表项：包含图片、名称、价格 -->`。
- **JS 注释**：接口调用、复杂函数需加注释，示例：
  ```javascript
  // 获取用户信息接口调用（无需传参，依赖全局token）
  // 返回值：Promise对象，resolve为{name: string, avatar: string, phone: string}，reject为错误信息
  getUserInfo() {
      return new Promise((resolve, reject) => {
          uni.request({
              url: 'https://example.com/api/user/info',
              method: 'GET',
              header: { 'Authorization': uni.getStorageSync('token') },
              success: (res) => {
                  if (res.data.code === 200) resolve(res.data.data);
                  else reject(res.data.msg); // 捕获接口业务错误
              },
              fail: (err) => {
                  reject('网络错误，请重试'); // 捕获网络错误
              }
          });
      });
  }
  ```
- **CSS 注释**：标注样式模块功能，如`/* 商品卡片：默认样式，hover时加阴影 */`。


## 二、样式语言规范
UniApp 中优先使用`scss`（支持变量、嵌套，便于维护），其次可使用`css`、`less`；通过`scoped`属性让样式仅作用于当前组件，避免样式污染。

### （一）属性顺序
按“位置属性 → 大小属性 → 文字系列 → 背景边框 → 其他（动画/过渡）”排列，示例：
```scss
.btn {
  /* 1. 位置属性 */
  position: relative;
  top: 0;
  left: 0;
  z-index: 1;
  display: flex;

  /* 2. 大小属性 */
  width: 200rpx;
  height: 80rpx;
  padding: 0 20rpx;
  margin: 10rpx 0;
  box-sizing: border-box; /* 确保padding不影响最终宽高 */

  /* 3. 文字系列属性 */
  font-size: 32rpx;
  font-weight: 500;
  line-height: 80rpx; /* 与height一致，实现文字垂直居中 */
  color: #fff;
  text-align: center;

  /* 4. 背景边框属性 */
  background: #007aff;
  border: none;
  border-radius: 40rpx;

  /* 5. 其他属性 */
  transition: background 0.3s;
}
.btn:hover {
  background: #0066cc;
}
```

### （二）属性使用缩写
尽量使用 CSS 属性简写，简化代码（需保证语义清晰）：
- `padding`/`margin`：`padding: 10rpx 20rpx`（上下 10rpx，左右 20rpx）、`margin: 5rpx`（四方向均 5rpx）。
- `font`：`font: 500 32rpx/80rpx sans-serif`（权重、大小/行高、字体）。
- `background`：`background: #007aff url('btn-bg.png') no-repeat center`。
- `border`：`border: 2rpx solid #eee`。
- 颜色代码：优先用简写（如`#fff`代替`#ffffff`、`#f00`代替`#ff0000`）。

### （三）去掉小数点前面的 0
数值小于 1 时，省略小数点前的 0，如`.9s`（代替`0.9s`）、`.5rpx`（代替`0.5rpx`），保持代码简洁。

### （四）CSS 的属性穿透使用
当需要修改子组件/第三方组件（如 UniUI、Vant）内部样式时，使用属性穿透（`scoped`样式下生效），三种常用方式：
1. **`::v-deep`**：Vue 官方推荐写法，兼容性较好（支持 H5、小程序），示例：
   ```scss
   ::v-deep .u-button__text {
     color: #007aff; /* 修改 UniUI 按钮的文字颜色 */
   }
   ```
2. **`/deep/`**：与`::v-deep`功能一致，但部分低版本浏览器兼容性较差，不推荐优先使用。
3. **`:deep()`**：Vue3 推荐的新写法（UniApp 3.x+ 支持），语法更简洁，示例：
   ```scss
   :deep(.van-cell__value) {
     color: #999; /* 修改 Vant 单元格的右侧文字颜色 */
   }
   ```

> 注：穿透样式尽量精准定位类名（避免用`div`、`span`等通用标签），防止影响其他组件样式；仅在必要时使用穿透，优先通过组件自身`props`修改样式。


## 三、数据绑定与事件处理规范
### （一）数据绑定
遵循 Vue 数据绑定规则，保证数据流向清晰：
1. **属性绑定**：用`v-bind`（简写`:`）绑定属性，如`<image :src="item.imgUrl" :class="{ active: isSelected }">`。
2. **文本绑定**：用`{{}}`插值语法绑定文本，如`<text>{{ item.name }}</text>`；避免在插值中写复杂逻辑（优先用`computed`）。
3. **双向绑定**：表单元素（如输入框、选择器）用`v-model`实现双向绑定，如`<input v-model="form.phone" placeholder="请输入手机号">`；如需自定义绑定逻辑，用`v-model`的修饰符（如`v-model.trim`去除空格）或`@input`事件。

### （二）事件处理
1. **事件绑定**：用`v-on`（简写`@`）绑定事件，方法名用`kebab-case`（如`@click="handle-submit"`），避免与系统事件重名。
2. **事件传参**：需传参时，直接在方法中传递，如`<view @click="handleClick(item.id)">点击</view>`，方法中接收参数：
   ```javascript
   methods: {
     handleClick(goodsId) {
       console.log('商品ID：', goodsId);
     }
   }
   ```
3. **事件触发**：子组件向父组件传值时，用`this.$emit('事件名', 参数)`，父组件用`@事件名`接收，如：
   ```javascript
   // 子组件：触发自定义事件
   this.$emit('add-cart', this.goodsId);
   // 父组件：接收事件
   <goods-card @add-cart="handleAddCart"></goods-card>
   methods: {
     handleAddCart(goodsId) {
       // 处理加购逻辑
     }
   }
   ```
4. **避免事件嵌套**：复杂事件逻辑（如接口调用+跳转）需提取到`methods`中，避免在模板中写内联逻辑（如`@click="() => { submit(); jump(); }"`），提升可维护性。


## 四、多端兼容规范
UniApp 需适配小程序（微信、支付宝等）、H5、App 多端，需通过条件编译、资源适配保证各端体验一致。

### （一）平台特定代码处理
使用 UniApp 条件编译语法，为不同平台编写专属代码（语法：`// #ifdef 平台标识`-代码-`// #endif`）：
1. **JS 代码**：区分平台执行逻辑，如 H5 需处理跨域、小程序需处理授权：
   ```javascript
   // #ifdef H5
   // H5 平台：处理跨域请求（如添加 withCredentials）
   uni.request({
     url: 'https://example.com/api/data',
     withCredentials: true,
     success: (res) => {}
   });
   // #endif

   // #ifdef MP-WEIXIN
   // 微信小程序：调用微信授权接口
   wx.getSetting({
     success: (res) => {
       if (!res.authSetting['scope.userInfo']) {
         wx.authorize({ scope: 'scope.userInfo' });
       }
     }
   });
   // #endif
   ```
2. **组件代码**：不同平台显示不同组件，如 H5 用`web-view`、小程序用原生组件：
   ```html
   <!-- #ifdef H5 -->
   <web-view :src="url"></web-view>
   <!-- #endif -->

   <!-- #ifdef MP-WEIXIN -->
   <view class="mini-program-content">{{ content }}</view>
   <!-- #endif -->
   ```
3. **样式代码**：适配不同平台样式差异（如小程序导航栏高度、H5 滚动条）：
   ```css
   /* #ifdef MP-WEIXIN */
   /* 微信小程序：适配顶部导航栏高度（避免内容被遮挡） */
   .page-content {
     padding-top: 88rpx;
   }
   /* #endif */

   /* #ifdef H5 */
   /* H5：隐藏页面滚动条 */
   ::-webkit-scrollbar {
     display: none;
   }
   /* #endif */
   ```

> 常用平台标识：`MP-WEIXIN`（微信小程序）、`H5`（H5 端）、`APP-PLUS`（App 端）、`MP-ALIPAY`（支付宝小程序）。

### （二）资源加载与适配
1. **图片资源**：
   - 小程序禁止在 CSS 中使用本地背景图（需用网络图片或 base64），H5 支持本地背景图。
   - UniApp 中，H5 端小于 4kb 的图片会自动转 base64（减少请求数）；若需关闭该功能，在`vue.config.js`中配置：
     ```javascript
     module.exports = {
       chain