## 利用rsync往线上测试机推送build后的代码

### 背景

在开发中我们通常会把前端的build后的代码推到测试环境供qa回归，

+ 通常的方案就是利用线上构造系统编译然后推送到测试环境；
+ 如果没有线上构造系统，也有人在本地编译构造提交到master，然后利用jekins拉去master的指定dist目录下的文件
+ 上面两种方案都比较正规军，如果只是用于qa完全可以直接推


### rsync

[rsync](https://www.npmjs.com/package/rsync)可以指定`source`和`destination`允许我们从本地往远程机器同步代码，正好能满足我们的需求


### package.json中的配置


```
 "scripts": {
        "start": "PRO_ENV=DEV node scripts/start.js",
        "build": "PRO_ENV=PRD node scripts/build.js",
        "build:dev": "PRO_ENV=DEV node scripts/build.js",
        "sync:dev": "node scripts/rsync.js ./scripts/dev.json",
        "deploy:dev": "sh scripts/deploy.dev.sh",
        "test": "node scripts/test.js --env=jsdom"
    },
    
 ```
 
 + [rsync.js](https://github.com/zuopf769/notebook/blob/master/fe/%E5%88%A9%E7%94%A8rsync%E5%BE%80%E7%BA%BF%E4%B8%8A%E6%B5%8B%E8%AF%95%E6%9C%BA%E6%8E%A8%E9%80%81build%E5%90%8E%E7%9A%84%E4%BB%A3%E7%A0%81/rsync.js)
 + 配置信息在[dev.json](https://github.com/zuopf769/notebook/blob/master/fe/%E5%88%A9%E7%94%A8rsync%E5%BE%80%E7%BA%BF%E4%B8%8A%E6%B5%8B%E8%AF%95%E6%9C%BA%E6%8E%A8%E9%80%81build%E5%90%8E%E7%9A%84%E4%BB%A3%E7%A0%81/dev.json)中配置
 + 每次需要登录密码

### 自动登录系统并执行任务
```
npm run build:dev;
expect -c "set timeout -1;
    spawn npm run sync:dev
    expect {
        *(yes/no)* {send -- yes\r;exp_continue;}
        *assword:* {send -- rgkcpekrisl1uouSQe1l\r;exp_continue;}
        eof        {exit 0;}
    }";

```

### 参考文献

[自动交互脚本之expect使用介绍](http://blog.51cto.com/balich/1720804)

