![](https://s2.ax1x.com/2019/09/03/nAr139.png)

# m-fe-vtw

m-fe-vtw 是 [fe-boilerplates](https://github.com/wx-chevalier/fe-boilerplates) 的一部分，React 版本参阅 [m-fe-rtw](https://github.com/wx-chevalier/m-fe-rtw)。

在 [Web 开发导论/微前端与大前端](https://github.com/wx-chevalier/Web-Series)一文中，笔者简述了微服务与微前端的设计理念以及微前端的潜在可行方案。微服务与微前端，都是希望将某个单一的单体应用，转化为多个可以独立运行、独立开发、独立部署、独立维护的服务或者应用的聚合，从而满足业务快速变化及分布式多团队并行开发的需求。如康威定律(Conway’s Law)所言，设计系统的组织，其产生的设计和架构等价于组织间的沟通结构；微服务与微前端不仅仅是技术架构的变化，还包含了组织方式、沟通方式的变化。微服务与微前端原理和软件工程，面向对象设计中的原理同样相通，都是遵循单一职责(Single Responsibility)、关注分离(Separation of Concerns)、模块化(Modularity)与分而治之(Divide & Conquer)等基本的原则。

![微前端项目结构](https://user-images.githubusercontent.com/5803001/44003230-de68ac5c-9e81-11e8-81f5-8092f7a9b421.png)

当我们考量项目框架、模板或者脚手架的时候，首先想到的点就是希望尽可能对上层屏蔽细节，但是对于长期维护的、多人协作的中大型项目而言，如果项目的主导者直接使用了部分抽象的脚手架，不免会给未来的更新、迭代带来一定的技术负债；同时，目前也有很多成熟的工程化脚手架，因此笔者选择以项目模板的形式抽象出微前端中所需要的部分。尽可能地遵循简约、直观的原则，减少抽象/Magic Function 等；大型项目可能会抽象出专用的开发工具流，但是对于大部分项目而言，在现有框架/工具链的基础上进行适当封装会是较优选择。

```sh
# 拉取并且提取出子项目
$ git clone https://github.com/wx-chevalier/m-fe-vtw

# 添加全局的依赖更新工具
$ yarn global add npm-check-updates

# 为各个子项目安装依赖，以及链接各个子项目
$ npm run bootstrap && npm run build

# 执行预编译操作
$ npm start
```

值得说明的是，微前端作为概念对于不同人承载了不同的考量，其实现方式、落地路径也是见仁见智，若有不妥，敬请指教。

## Features

- 状态管理，灵活支持 Redux/MobX/Dva 等不同的状态管理框架，对于 Redux 提供全局统一的 Store 声明。

- 模块分割，非 APP 类可单独发布，APP 类可单独运行，与发布。发布版本可包含 ES, CJS, UMD 等，dist 目录下包含 ES/CJS 模块，build 目录下包含 APP 完整资源以及 UMD 模块。

- 应用编排：版本控制、应用注册、应用路由，子应用资源不使用 Hash 方式，而是使用语义化版本，`/[cdnHost]/[projectName]/[subAppName]/[x.y.z]/index.{js,css}`。

- 动态主题与样式切换，- 样式，LESS 文件支持 CSS Modules，CSS/SCSS 使用标准 CSS。

- 权限控制

- 路由与导航框架

- 国际化

- PWA

- 服务端渲染

## Nav | 关联项目

- [react-snippets](https://github.com/wx-chevalier/react-snippets): React Snippets(.ts/.tsx), about design patterns/techniques used while developing with React and TypeScript.

- [vue-snippets](https://github.com/wx-chevalier/vue-snippets): Vue Snippets(.js/.ts), about design patterns/techniques used while developing with Vue and JavaScript/TypeScript.

- [m-fe-husky-config](https://github.com/wx-chevalier/m-fe-configs)：Common Dev Configs(ESLint, Prettier, Husky, etc.) for Micro-Frontend Apps

- [m-fe-rtw](https://github.com/wx-chevalier/m-fe-rtw): Micro-Frontend boilerplate with React & TypeScript & Webpack, for complicated cooperative applications. | 微前端项目模板

- [m-fe-vtw](https://github.com/wx-chevalier/m-fe-vtw): Micro-Frontend boilerplate with Vue & TypeScript & Webpack, for complicated cooperative applications. | 微前端项目模板

- [fractal-components](https://github.com/wx-chevalier/fractal-components): Massive Fractal Components in Several Libraries(Vanilla, React, Vue, Weapp), for building your great apps easily again

- [Legoble](https://github.com/wx-chevalier/Legoble): Build your apps like stacking Lego blocks 💫 总想自己实现一款可视化配置的动态应用构建工具，动态表单、动态布局、动态报告、动态规则、动态选择、动态流程

# 项目开发

笔者一直推崇[渐进式的工程架构](https://parg.co/rAn)，因此该模板对于复杂度要求较低的项目而言，可以直接从基础模式启动，与其他 TS 项目并无太大区别。

## Structure | 项目结构

完整的微前端应用，可能会包含以下组成部分：

- Module | 模块: 模块是可单独编译、发布的基础单元，基础模式下可直接打包入主应用，标准模式下多项目共用时可单独打包为 AMD/UMD 格式，通过 SystemJS 引入
- Page | 页面: 页面不可单独编译，使用 Webpack SplitChunk 或其他机制进行异步加载
- App | 应用: 应用是对模块的扩展，是实际用户可见的部分
- Widget | 控件: 控件是特殊的模块，譬如通用的无业务组件等
- Extension | 扩展: 扩展是特殊的应用，提供了跨模块的通用功能，类似于 Chrome Extension 的定位

基于此，我们可以将某个微前端应用抽象为如下不同的模块组：

基础模块：

- rtw: 根目录，public 目录下包含了部分跨模块集成测试的代码

核心模块：

- rtw-core/rtw-sdk/rtw-shared: 暴露给子应用可用的通用基础类、模型定义、部分无界面独立模块等。rtw-core 建议不放置界面相关，使用 Jest UT 方式进行功能验证。
- rtw-bootstrap: 完整项目级别编译与启动入口，包含项目的运行时配置、依赖配置\消息总线、注册中心、核心模块加载机制等。
- rtw-host-app: 提供界面基础容器，譬如应用标准的 Layout，Menu 等组件；提供 Redux 核心 Store。

子业务应用：

- rtw-mobx-app: MobX 示例应用
- rtw-redux-app: Redux 示例应用

扩展模块：

- rtw-widgets: 包含部分业务型控件，提供给所有的子应用使用，提取通用业务逻辑、对上屏蔽部分第三方依赖关系，类似于完整的 OSS 文件上传控件等。
- rtw-extensions: 包含部分业务无关的通用型插件，类似于 Chrome Extension 的定位。
- rtw-worker: 包含通用的 Web Worker & WASM 计算模块，子应用内也可以通过 Buffer 方式直接引入自定义的 Worker

如果希望在子应用 A 中加载子应用 B 的实例，则应该使用类似于依赖注入的方式，从统一的注册中心中获取该实例对象。所有各个模块共享的基础库，都必须以 UMD 模式加载到全局；rtw-host-app 中声明与使用需要展示哪些模块，rtw-bootstrap 中注册可提供的 UMD 子模块。

项目使用 Webpack 作为项目的配置打包工具，同样遵循透明原则；所有的子模块中的 Webpack 配置文件是引用并根据自身需求修改之后的根目录下的 scripts 中的配置文件。值得一提的是，根据 Node 的模块索引规则，scripts 目录下的配置文件会引用根目录下的 node_modules 文件，因此同样需要在根目录下安装 Webpack 及相关的依赖。

## 基础模式

基础模式类似于(伪)多模块单页面，仅有唯一的 Host APP 作为编译与运行的入口，其他包体（譬如 rtw-core）直接打包进主包体中，不使用 SystemJS 进行独立加载。

### `rtw-core`

rtw-core 及相似的库承载了公共的结构定义、工具类等，在该包体目录下运行 `npm run build` 命令即可以生成 ES/CJS/UMD 等多种类型文件，以及 types 类型定义；可以直接通过 npm publish 来发布到公共/私有的 NPM 仓库中。

其他包体通过 NPM 安装 rtw-core 并使用，如果以标准模式运行，则需要首先加载该库到全局作用域，利用 RequireJS/SystemJS 等工具遵循 AMD 规范来注入到其他依赖的库/应用中。

值得一提的是，对于子应用中，如果存在需要共享组件/类型的情景。对于类型信息，建议是将子应用同样编译打包发布到 NPM 仓库中，纯组件可以直接引入，对于业务组件建议通过全局的注册中心来获取。

### `rtw-host-app`

在 rtw-host-app 包下，执行 `npm run dev:sa` 命令，会从 `src/index.sa` 文件启动应用；如上文所述，该模式仅会基于 Webpack Splitted Chunk 进行异步加载，其开发流程与标准的单模块应用并无区别。

## 标准模式

### `rtw-bootstrap & rtw-host-app`

rtw-bootstrap 是微前端应用的实际启动点，其核心功能是执行依赖与子应用的注册。在启动时，其会根据传入的 `__HOST_APP__` 与 `__DEV_APP__` 等变量信息完成应用的顺序加载与启动。在标准模式下，rtw-host-app 的入口是 `src/index` 文件，该模式下，index 文件会对外暴露 render 函数，该函数会由 rtw-bootstrap 注入 importApp 函数来执行子应用加载：

```ts
export function render(_importApp: Function) {

  importApp = _importApp;

  ReactDOM.render(
    ...
  );
}
```

换言之，rtw-bootstrap 提供了应用加载的能力，而 rtw-host-app 决定了应该加载哪些应用；在实际案例中，我们应该将用户权限控制、菜单与子应用信息获取等业务操作放置在 rtw-host-app 中。

### `rtw-redux-app & rtw-mobx-app`

这里以 rtw-mobx-app 为例介绍如何进行子应用开发，如果是项目已经发布上线，那么我们可以通过 Resource Overrides 等在线资源请求转发的工具将线上资源请求转发到本地服务器。在进行本地开发时，因为子应用本身并不会包含 `ReactDOM.render` 或者类似的将 Virtual DOM 渲染到界面的函数，因此在运行 `npm run dev` 之后，本地会开启生成 UMD 文件的 Webpack Dev Server。参考子应用的 `public/index.html` 文件：

```html
<script src="./bootstrap/static.js" type="text/javascript"></script>
<script src="./bootstrap/runtime.js" type="text/javascript"></script>
<script src="./bootstrap/vendors.js" type="text/javascript"></script>

<script>
  // 联调环境
  //   window.__HOST_APP__ = {
  //     id: 'host',
  //     name: 'HOST APP',
  //     module: 'http://0.0.0.0:8081/index.js',
  //     css: 'http://0.0.0.0:8081/index.css'
  //   };

  // 正式开发环境
  window.__HOST_APP__ = {
    title: 'HOST APP',
    module: '/release/rtw-host-app/index.js',
    css: '/release/rtw-host-app/index.css'
  };

  window.__DEV_APP__ = { id: 'dev', name: 'DEV APP', module: '/index.js' };
</script>
<script src="./bootstrap/index.js" type="text/javascript"></script>
```

可以看出子应用的启动需要依赖于 rtw-bootstrap 以及 rtw-host-app，如果项目已经发布上线，那么建议是直接从 CDN 加载资源；否则可以将资源放置到 `public/release` 目录下。如果本地需要同时调试 Host APP，则直接也将 Host APP 以开发方式运行（`npm run dev`），然后直接引入 Webpack Dev Server 生成的资源地址即可。

# About

## ChangeLog

- [x] 使用 ESLint 替换 TSLint
- [ ] 将现有项目中的通用配置抽出到 m-fe-configs 中

## Home & More

[某熊的技术之路指北 ☯ ](https://github.com/wx-chevalier/Developer-Zero-To-Mastery)就是对笔者不同领域方面沉淀下的知识仓库的导航与索引，便于读者快速地寻找到自己需要的内容。路漫漫其修远兮，吾正上下而求索，也希望能给所有遇见过笔者痕迹的同学些许帮助，在浩瀚银河间能顺利达到一个又一个彼岸。Just Coder，Travel in Galaxy，欢迎关注[某熊的技术之路](https://i.postimg.cc/mDxdH0VL/image.png)公众号，让我们一起前行。

![](https://i.postimg.cc/59QVkFPq/image.png)

您可以通过以下导航来在 Gitbook 中阅读笔者的系列文章，涵盖了技术资料归纳、编程语言与理论、Web 与大前端、服务端开发与基础架构、云计算与大数据、数据科学与人工智能、产品设计等多个领域：

- 知识体系：《[Awesome Lists | CS 资料集锦](https://ngte-al.gitbook.io/i/)》、《[Awesome CheatSheets | 速学速查手册](https://ngte-ac.gitbook.io/i/)》、《[Awesome Interviews | 求职面试必备](https://github.com/wx-chevalier/Awesome-Interviews)》、《[Awesome RoadMaps | 程序员进阶指南](https://github.com/wx-chevalier/Awesome-RoadMaps)》、《[Awesome MindMaps | 知识脉络思维脑图](https://github.com/wx-chevalier/Awesome-MindMaps)》、《[Awesome-CS-Books | 开源书籍（.pdf）汇总](https://github.com/wx-chevalier/Awesome-CS-Books)》

- 编程语言：《[编程语言理论](https://ngte-pl.gitbook.io/i/)》、《[Java 实战](https://ngte-pl.gitbook.io/i/java/java)》、《[JavaScript 实战](https://ngte-pl.gitbook.io/i/javascript/javascript)》、《[Go 实战](https://ngte-pl.gitbook.io/i/go/go)》、《[Python 实战](https://ngte-pl.gitbook.io/i/python/python)》、《[Rust 实战](https://ngte-pl.gitbook.io/i/rust/rust)》

- 软件工程、模式与架构：《[编程范式与设计模式](https://ngte-se.gitbook.io/i/)》、《[数据结构与算法](https://ngte-se.gitbook.io/i/)》、《[软件架构设计](https://ngte-se.gitbook.io/i/)》、《[整洁与重构](https://ngte-se.gitbook.io/i/)》、《[研发方式与工具](https://ngte-se.gitbook.io/i/)》

* Web 与大前端：《[现代 Web 开发基础与工程实践](https://ngte-web.gitbook.io/i/)》、《[数据可视化](https://ngte-fe.gitbook.io/i/)》、《[iOS](https://ngte-fe.gitbook.io/i/)》、《[Android](https://ngte-fe.gitbook.io/i/)》、《[混合开发与跨端应用](https://ngte-fe.gitbook.io/i/)》

* 服务端开发实践与工程架构：《[服务端基础](https://ngte-be.gitbook.io/i/)》、《[微服务与云原生](https://ngte-be.gitbook.io/i/)》、《[测试与高可用保障](https://ngte-be.gitbook.io/i/)》、《[DevOps](https://ngte-be.gitbook.io/i/)》、《[Node](https://ngte-be.gitbook.io/i/)》、《[Spring](https://ngte-be.gitbook.io/i/)》、《[信息安全与渗透测试](https://ngte-be.gitbook.io/i/)》

* 分布式基础架构：《[分布式系统](https://ngte-infras.gitbook.io/i/)》、《[分布式计算](https://ngte-infras.gitbook.io/i/)》、《[数据库](https://ngte-infras.gitbook.io/i/)》、《[网络](https://ngte-infras.gitbook.io/i/)》、《[虚拟化与编排](https://ngte-infras.gitbook.io/i/)》、《[云计算与大数据](https://ngte-infras.gitbook.io/i/)》、《[Linux 与操作系统](https://ngte-infras.gitbook.io/i/)》

* 数据科学，人工智能与深度学习：《[数理统计](https://ngte-aidl.gitbook.io/i/)》、《[数据分析](https://ngte-aidl.gitbook.io/i/)》、《[机器学习](https://ngte-aidl.gitbook.io/i/)》、《[深度学习](https://ngte-aidl.gitbook.io/i/)》、《[自然语言处理](https://ngte-aidl.gitbook.io/i/)》、《[工具与工程化](https://ngte-aidl.gitbook.io/i/)》、《[行业应用](https://ngte-aidl.gitbook.io/i/)》

* 产品设计与用户体验：《[产品设计](https://ngte-pd.gitbook.io/i/)》、《[交互体验](https://ngte-pd.gitbook.io/i/)》、《[项目管理](https://ngte-pd.gitbook.io/i/)》

* 行业应用：《[行业迷思](https://github.com/wx-chevalier/Business-Series)》、《[功能域](https://github.com/wx-chevalier/Business-Series)》、《[电子商务](https://github.com/wx-chevalier/Business-Series)》、《[智能制造](https://github.com/wx-chevalier/Business-Series)》

此外，你还可前往 [xCompass](https://wx-chevalier.github.io/home/#/search) 交互式地检索、查找需要的文章/链接/书籍/课程；或者在 [MATRIX 文章与代码索引矩阵](https://github.com/wx-chevalier/Developer-Zero-To-Mastery)中查看文章与项目源代码等更详细的目录导航信息。最后，你也可以关注微信公众号：『**某熊的技术之路**』以获取最新资讯。
