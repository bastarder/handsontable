# Handsontable 6.2.2
> 组件基于 `Handsontable 6.2.2` 进行二次开发，修复一些已知的问题;


## 1. 原组件库官方文档
[handsontable.com/docs/6.2.2](https://handsontable.com/docs/6.2.2/tutorial-quick-start.html)

## 2. 修改记录

### 修复引用类型数据单元格无法AutoFill的问题
> 问题原因：`src/core.js line:770` 附近相关代码，是判断 单元格新旧数据，是否需要发生变更的地方，在旧代码中仅仅取了 `orgValue[0] || orgValue`,而数组类型初始为空数组, 修改以后变为`['A']`则判断相同的参数就变成了 `orgValue[0]`即`A`，所以引用类型的判断有误，应该改为`['A']`数组本身来作为对比凭证。

> 修复相关commit：`ac7c995c87230ebb3e34928d990fc737bde534dd`
	
> 涉及文件: `src/core.js`


### 修复在fixColumn进行wheel的时候报错的问题
> 问题原因：`Unable to preventDefault inside passive event listener due to target being treated as passive`, 这是因为在新浏览器中，对滑动有优化，简单来说，因为原有的滑动事件中当浏览器监听到滑动的时候 他不能立马开始滚动页面，因为他不知道 你会不会在哪里取消这个事件的默认操作，所以添加了一个参数，passive来断定，这个事件不会被取消默认操作，来优化滑动。但表格组件没有考虑这个属性的存在，所以在一些浏览器默认优化的情况，执行preventDefault，就会抛出异常，只要让表格组件 监听事件的方法支持第三个参数即可。

> 修复相关commit：`d967c32c1fe982d469f337844e4d9e9a37e75bae`

> 涉及文件: `src/eventManager.js`,`src/3rdparty/walkontable/src/overlays.js`,`src/helpers/feature.js`, 其中`eventManager.js`就是封装的事件监听方法，让其支持第三个参数，然后在`overlays.js `发起监听的地方传递参数, `feature.js`中添加一个用于判断是否支持passive的兼容性判断方法。

### 修复表格的滚动事件无法冒泡到外层页面的问题
> 问题原因：两种情况，但现象是一致的，当表格没有固定高度的时候，在表格上滚动，事件不会冒泡到外部，当表格有固定高度时，在表格上滚动，滚动到表格顶部后继续滚动，事件也无法冒泡到外部，导致无法触发页面的滚动，这样体检极差，所以解决方法是应该在表格无需滚动的时候，将事件传递出去，来防止无法滚动外部页面的问题。

> 修复相关commit：`f3509a590e1e73b18f035af4b016400775c8d350`, `3fe591d41064553a8d4b10cd86d7ba344514e23f`

> 涉及文件: `test/helpers/common.js`, `src/editors/handsontableEditor.js`, `src/3rdparty/walkontable/src/overlay/bottom.js`, `src/3rdparty/walkontable/src/settings.js`, `src/3rdparty/walkontable/src/overlays.js`, `src/3rdparty/walkontable/css/walkontable.css`, `src/tableView.js`, `src/defaultSettings.js`, `handsontable.d.ts`

