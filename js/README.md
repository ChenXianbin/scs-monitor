# 前端监控系统<sup>monitor</sup>上报插件V2.0

### 使用方法

- js_monitor.js 兼容低版本浏览器IE8+
- monitor.js 高版本浏览器使用
在项目里引用上面两个文件中的其中一个：

```
<script type="text/javascript" src="js_monitor.js"></script>
```

#### 初始化init

```
monitor.init({
    code: 'scscms', // 上报的code标识
    uin: '', // 用户标识
	prevent:false, //是否禁止捕获（开发时可设为true）
    key:'monitor',//localStorage key
    url:'http://localhost:8000/api/beacon', //上报接口（beacon或report）
    ignore: [], // 忽略某些关键词错误, 支持String或Regexp
    random: 1 // 抽样 (0-1] 1-全量
})
```

- code:就是监控网站标识，让后台知道是哪个网站上报的错误。请参见server/common.js  sort_type配置
- uin:被监控网站所登录的用户（可选），为方便追踪错误来源及还原bug操作。
- key:存储localStorage的key，可任意修改。
- url:上报日志地址（重要）
- ignore:忽略某些关键词错误, 支持String或Regexp。
- random:抽样上报(0-1] 1-全量。

#### 新增队列日志

```
monitor.push({
    title: '错误标题',
    info: '错误详情',
    url:'错误来源页（可选）为空时自动取location.href值'
})
```
#### 捕捉try错误日志：

```
try{
    let a = b + 1;
    if(a){
        throw Error("手动抛出错误");
    }
}catch(e){
    this.$monitor.push({
        title: e.message,
        info: e.stack
    });
    console.error('捕捉有误：',e.stack)
}
```
注意：此处默认获取js自身错误。如果需要手动抛出错误，请使用`throw Error("something");`而不要使用`throw "something";`

#### 添加API请求耗时日志（新）：

```
monitor.push({
    title: 'API:'+url.match(/(?:.*\/)*([^?]+)/)[1],
    url: url,
    info: time
});

```

- url:请求的API接口地址
- time:请求API耗时（毫秒）

#### 立刻上报日志beacon方法(不推荐)

```
monitor.beacon(obj?)
```

`V2.0版本由init初始化函数里自动判断是否需要上报，并且已经删除report方法`

#### beacon方法说明

beacon使用的是浏览器[navigator.sendBeacon](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)接口，不支持IE等低版本浏览器。具有不阻塞网站加载效果。

---

### vue版本vue_monitor.js

#### 使用方法：

```
import monitor from './js/vue_monitor'
Vue.use(monitor);
```

#### 调用方法：

参考上面方法，把`monitor`替换成`this.$monitor`即可。
