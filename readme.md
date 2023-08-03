# 基于vue3的无缝自动滚动


## 代码演示
### [在线demo](http://workeatsleep.gitee.io/vue3-demo/#/)
```javascript
import AutoScrollSeamless from 'auto-scroll-seamless';
  // 竖向
  <div style="height:500px">
    <AutoScrollSeamless>
      <div v-for="item in 20" style="height:50px;border:1px solid red"></div>
    </AutoScrollSeamless>
  </div>
  // 横向
  <AutoScrollSeamless :step="0.5" mode="horizontal">
    <div v-for="item in 15" style="height:50px;border:1px solid red;width:100px"></div>
  </AutoScrollSeamless>
```


## API

### Props

|     属性      |       说明       |        类型        | 默认值 |
| :-----------: | :--------------: | :----------------: | :----: |
|      width      | 容器宽度（默认为外层宽度） |       `string`       |   "100%"   |
| height |  容器高度（默认为外层高度）  | `Array<string>` |   []   |
| mode |  滚动方向  | `vertical | horizontal` |   `horizontal`   |
| step |  每帧移动像素  | `Number` |   `1`   |