# 介绍

该骨架是我们团队通过开发业务上的 React SSR 应用提取出来的一个基础的 Egg + React + SSR 应用的实现，本文档会详细介绍一个完整的 Egg + React + SSR 应用的开发流程, 我们力求该应用的实现方式比目前市面上任何框架的实现方式都要简单，同时做到本文档详细程度超过目前市面上的任何一篇文章并且涉及到一些源码讲解，不但教你如何做，还会教你为什么要这样做以及原理是什么。

## 初衷

由于 React 官方缺少完整的 SSR(Server-Side Rendering)文档，只是简单的介绍了一下需要用到的 API, 同时网络上的文章也良莠不齐，大都是十分简略的实现方式，无法适用于大规模的线上应用。

本文档假设你已经熟悉 React 本身，并且具有 Node.js 和 webpack 的一定使用经验。即使不多也没关系，我们尽量以通俗易懂的方式来将这些代码解释的很详细。我们在 Serverless 场景下的[SSR 框架](https://github.com/ykfe/ssr)已经正式发布，如果你更喜欢平滑开箱即用体验的更高层次解决方案并且希望能够快速部署，推荐使用该框架进行开发。
但是，如果你需要更直接地控制应用程序的结构，它并不适合这种使用场景。无论如何，阅读本文档将更有助于更好地了解一切如何运行。

最后，请注意，本文档中的解决方案不是限定的,但是经过多次优化我们发现它们对我们来说很好，但这并不意味着无法继续改进。可能会在未来持续改进，欢迎提交 [pull request](https://github.com/ykfe/egg-react-ssr/pulls) 作出贡献！

## 技术栈

[Egg](https://eggjs.org/zh-cn/intro/index.html)企业级 Node.js 框架, 基于 Koa 开发，性能优异,高度可扩展的插件机制,内置多进程管理

[React](https://react.docschina.org/)目前最流行的前端框架之一

[ReactDOMServer](https://reactjs.org/docs/react-dom-server.html)React 官方提供的服务端渲染有关的库

## 执行环境

- 服务器 Node.js >= 7.6， 为了原生的使用 async/await 语法
- 浏览器版本大于等于 IE9, React 支持到 IE9，但为了更好的在 IE 下使用，你可能需要引入[Polyfill](https://reactjs.org/docs/javascript-environment-requirements.html)

## 功能/特性

以下是该应用具有的功能点

### 已完成

- 基于 cra 脚手架开发，由 cra 开发的 React App 可无缝迁移，如果你熟悉 cra 的配置，上手成本几乎为 0
- 小而美，相比于 beidou，next.js 这样的高度封装方案，我们的实现原理和开发模式一目了然
- 推荐使用 egg 作为 Node.js 框架但并不强制，事实上你可以发现几乎无需做任何修改即可迁移到 koa,nest.js 等框架
- 同时支持 SSR 以及 CSR 两种开发模式,本地开发环境以及线上环境皆可无缝切换两种渲染模式
- 统一前端路由与服务端路由，无需重复编写路由文件配置
- 支持切换路由时自动获取数据
- 支持本地开发 HMR
- 稳定性经过线上大规模应用验证，可提供性能优化方案
- 支持 tree shaking，优化构建 bundle 大小以及数量
- 支持 csr/ssr 自定义 layout，无需通过 path 来手动区分
- 抛弃传统模版引擎，拥抱 React 组件，使用 JSX 来作为模版
- 独创[最佳发布实践](http://ykfe.surge.sh/guide/deploy.html)，让你更新页面无需重启应用机器
- 配套结合[antd](https://github.com/ykfe/egg-react-ssr/tree/master/example/ssr-with-antd)的 example 的实现
- 配套结合[react-loadable](https://github.com/ykfe/egg-react-ssr/tree/master/example/ssr-with-loadable)做路由分割的 example 的实现
- 配套结合[ssr-with-multipage](https://github.com/ykfe/egg-react-ssr/tree/dev/example/ssr-with-multipage)多页面应用的 example
- 配套结合[dva](https://github.com/ykfe/egg-react-ssr/tree/master/example/ssr-with-dva)做数据管理的 example 的实现
- 配套阿里云 serverless [FC](https://github.com/ykfe/ssr-with-fc)版本的实现
- 配套[TypeScript](https://github.com/ykfe/egg-react-ssr/tree/dev/example/ssr-with-ts)版本的实现

## 与其他社区方案的对比

相较于目前市面上的其他实现方案，我们有诸多优点, 无论是本地开发还是线上发布。

### 与[easy-team](https://github.com/ykfe/egg-react-ssr/wiki/%E4%B8%8Eeasy-team%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%A1%88%E7%9A%84%E5%AF%B9%E6%AF%94)方案对比

- 与服务端框架不耦合，easy-team 的实现方式与 egg 框架耦合的太过紧密
- 本地开发读取服务端 bundle 的方式更加优雅
- 通过 config.default.js 同时两种渲染模式无缝切换而 easy-team 需要在构建时指定渲染类型

### 与[next.js](https://github.com/ykfe/egg-react-ssr/wiki/%E4%B8%8Enext.js%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%A1%88%E7%9A%84%E5%AF%B9%E6%AF%94)方案对比

- 与服务端框架不耦合，next.js 实现与 http 模块耦合紧密
- 本地开发读取服务端 bundle 的方式更加优雅
- 体积小，同等复杂度项目大小为为 next.js 生成文件的 0.3 倍
- 实现非黑盒，关键配置皆可通过 config.default.js 来配置
