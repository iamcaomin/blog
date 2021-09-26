# Webpack5 Module Federation

## Module Federation 是什么？

Module Federation 翻译出来是**模块联邦**（很僵硬的翻译，但是大家都这么说，那咱也只能按这个来了），用于跨应用间的**动态加载**代码，同时**共享依赖**。

先说动态加载，正常情况下，在开发阶段，加载远程的bundle/build文件；生产阶段呢，加载远程build后的文件。这两种文件如果你不知道有什么区别，可以打开开发者工具-Network查看对应的远程文件。当然如果你想在生产环境里，加载非生产环境的远程文件的话，有非常大的概率发生事故的！所以一定要非常谨慎谨慎谨慎配置正确的路径！怎么配置？后面会讲，别着急~

再说到共享依赖，共享咱都知道，但是是谁共享谁的？如果共享的依赖里，缺少了我需要的依赖要怎么办？举个🌰，假设A共享了依赖，B也共享了依赖，B使用了A的一个组件，那么这种情况下，A会共享B的依赖，如果B共享出的依赖没有满足A，那么A会自动拉取需要的依赖。相关配置有机会会再整理一下~

## 为什么要用Module Federation？

什么情况下我们需要用到Module Federation？为了解决什么问题？有没有其他的方法可以代替？

webpack5 Module Federation 发布也已经很久了，虽然对他垂涎不已，但始终没有尝试。这次也是因为项目需要使用**微前端**的方式来部署，自然而然的也需要把一个完整的项目分割成几个小项目，在做项目迁移的过程中，发现有些组件会在几个项目中同时使用，作为优秀的前端er，自然不能每个项目都 ctrl + c/v 制造**重复代码**了，就想到了咱今天的主角儿，在经过几天的踩坑的过程中，也算是完成了我的期望了🎉。

那除了module federation，还有没有其他的可替代方法呢？答案自然也是有的。比如 [externals](https://webpack.js.org/configuration/externals/) 或 [dll-plugin](https://webpack.js.org/plugins/dll-plugin/)，嗯，都是年长成熟的哥哥，externals还是挺常见的，直接在webpack的externals配置，常把一些依赖分割出来，运行时从外部获取扩展（比如通过 CDN）；dll-plugin 会根据配置，额外生成 manifest.json 文件，这个文件的作用是映射。

这两种方法自然是可行的，但是并不算真正意义上的共享，他们对跨应用间的组件（尤其是业务组件）共享并没有实际意义上的作用。

## 但是，为什么要用Module Federation呢？

毕竟是webpack5的新功能，自然我们用的时候，就是通过webpack的配置了。

```javascript
  // Webpack.config.js
  plugins: [
    new ModuleFederationPlugin({
      // 提供给其他服务加载的文件
      filename: "remoteEntry.js",
      // 唯一ID，用于标记当前服务
      name: "share",
      // Remote 中需要配置
      // library: { type: "var", name: 'share' },
      // 需要暴露的模块，使用时通过 `${name}/${expose}` 引入
      exposes: {
        "./NewsList": "./src/NewsList",
      },
      remote: {
        "share": "share@http://localhost:8001/remoteEntry.js",
      },
      shared: ["vue", "vuex"]
    })
  ]
  
```

> 先说小两个概念，Host 和 Remote：
>
> - Host, 消费其他 Remote 的项目
>
> - Remote，被 Host 消费的项目
>
> Host 可以只是 Host， 也可以是 Remote；Remote 亦然。他们之间没有主从关系，是自由人~

回到配置上，Host 和 Remote 的主要区别是 remote 和 exposes的配置：

> remote： Host 必填， 声明远程 Remote 路径，就是我使用的共享组件来自哪儿
>
> exposes: Remote 可选，声明需要共享的文件，可以是组件，也可以是一般的工具函数类文件


## 需要注意的地方

### vue2 中使用时，@vue/cli-service 版本要求

请务必升级到5.x，以及配套的依赖也升级到与之配备的版本。

### Remote 配置中的 publicPath

必须写死，不能配置相对路径或者默认，必须 **http://localhost:9000** 的完整路径！！！

> 注意是 Remote 的项目，Host 项目可以按照平时一样配置，如果 Host 同时也是 Remote，也要配置成绝对路径

下面是 publicPath 没有配置成完成路径的报错信息：

![remoteEntry.js 404](https://files.mdnice.com/user/18628/bc7ca70c-cd58-4550-b5e2-e674077df437.png)

![ScriptExternalLoadError: Loading script failed.](https://files.mdnice.com/user/18628/4641a4bf-c147-43ca-829e-899423f86070.png)


### Remote 中 library 配置

社区中有人建议配置成`library: { type: "var", name: 'your remote name' }`，但是如果不配置也没什么影响。

### Host 中如何使用共享组件？

通过`require('name/Component')`导入模块。


## 最后

webpack5 的 module federation 的功能对微前端的业务共享还是挺方便，如果每个模块都复制一份相同的代码，对后面维护的同学来说，简直就是噩梦！！

最后，希望对你有帮助~
