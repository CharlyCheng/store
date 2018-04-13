### 我们真的需要react-echarts插件吗？ 

### NO！！！

**在这里，我使用echarts提供的模块化加载方式，实现了几个react-echarts图表组件：[react-echarts图表在线渲染查看][1]**

你可以打开控制台，观察每个图表组件的加载情况。

但是，如果你认为仅仅是模块导入echarts的tooltip、grid这些插件就完事了，那么我也用不着写这篇文章分享了，下面会讲到另外一种异步加载方式。

导入echarts，最烦人的是什么？配置option是其一，其二就是极其庞大的echarts！特别是管理后台使用到echarts的时候，如果需要同个页面展示数十种图表类型，你就该好好考虑性能问题了。

### 插件版本号

```
  "echarts": "^3.6.2",
  "react": "^15.6.1",
  "react-dom": "^15.6.1"

```

### 实现了哪些图表组件

1、饼图
2、柱状图
3、折线图
4、散点图
5、地图
6、雷达图
7、k线图

### 我们不总是需要插件

实现这些echarts-react组件的目的，是告诉大家，react可以不需要引入第三方插件，使用echarts，不要惧怕react组件！

看过很多人说react难写，因为他们习惯了在jQuery开发模式下导入echarts、swiper、d3等插件。而突然切换到react中，就产生了不知所措的感觉。
如何在react中导入第三方插件，成为了他们心中的痛点，所以一些人就认为需要别人封装好的echarts-react插件或者其他react插件，才能使用，这种想法是错的。


### echarts体积太大，使用模块化加载

以柱状图为例子，我们根据需要渲染的插件采取模块导入，不渲染的组件不导入，最大程度减小js。

    import echarts from 'echarts/lib/echarts' //必须
    import 'echarts/lib/component/tooltip'
    import 'echarts/lib/component/grid'
    import 'echarts/lib/chart/bar'

### 组件化开发的福音，react组件模块化加载

demo中采用单个echarts组件异步打包加载的模式，因为echarts组件普遍偏大，即使压缩也效果不明显，所以异步加载是最好的方式。

    import { pieOption, barOption, lineOption, scatterOption, mapOption, radarOption, candlestickOption } from './optionConfig/options'
    const PieReact = asyncComponent(() => import(/* webpackChunkName: "PieReact" */'./EchartsDemo/PieReact'))  //饼图组件
    const BarReact = asyncComponent(() => import(/* webpackChunkName: "BarReact" */'./EchartsDemo/BarReact')) //柱状图组件
    const LineReact = asyncComponent(() => import(/* webpackChunkName: "LineReact" */'./EchartsDemo/LineReact'))  //折线图组件
    const ScatterReact = asyncComponent(() => import(/* webpackChunkName: "ScatterReact" */'./EchartsDemo/ScatterReact'))  //散点图组件
    const MapReact = asyncComponent(() => import(/* webpackChunkName: "MapReact" */'./EchartsDemo/MapReact'))  //地图组件
    const RadarReact = asyncComponent(() => import(/* webpackChunkName: "RadarReact" */'./EchartsDemo/RadarReact')) //雷达图组件
    const CandlestickReact = asyncComponent(() => import(/* webpackChunkName: "CandlestickReact" */'./EchartsDemo/CandlestickReact')) //k线图组件

### 启动项目

    //安装
    npm install
    
    //启动
    npm start

### 打包项目

    npm run build

### 实现方案介绍

1、每个图表单独封装成一个组件，通过参数传递数据，你会发现，图表内部代码几乎完全一样，只有import的类型不同。

2、异步加载是提高图表加载性能的最佳方式，不管是服务端还是客户端渲染。

3、在这些demo中，我认为对你来说最有价值的是react组件异步加载模式，很多人异步加载组件是通过拆分路由的方式，而非路由组件的异步加载，并不多人去尝试。但我想告诉你的是，
非路由组件的异步加载会将你的庞大的父组件拆分的更细，体积更小，加载的更加流畅。



===> [去往github查看react异步加载echarts源码][2]


  [1]: http://setcamp.tech
  [2]: https://github.com/hyy1115/react-echarts-modules