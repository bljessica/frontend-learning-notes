# echarts使用tip

## 背景

ECharts是一个使用 JavaScript 实现的开源可视化库，可以流畅的运行在 PC 和移动设备上，兼容当前绝大部分浏览器（IE8/9/10/11，Chrome，Firefox，Safari等），底层依赖矢量图形库 [ZRender](https://github.com/ecomfe/zrender)，提供直观，交互丰富，可高度个性化定制的数据可视化图表。

[官网](https://echarts.apache.org/zh/index.html)

## tips

### dataZoom

区域缩放如何手动触发还原（back）事件：

```javascript
// 找到属性名
let tmp = ''
	Object.keys(this.chart._componentsMap).map((item) => {
    if (item.indexOf('series') !== -1 && item.indexOf('_toolbox') !== -1) {
        tmp = item
    }
	})
// 手动触发 back 事件
if (this.chart._componentsMap[tmp]._features) {
	this.chart._componentsMap[tmp]._features.dataZoom.onclick(null, null, 'back')
}
```

