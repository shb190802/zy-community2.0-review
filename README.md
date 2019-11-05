# 中原智慧社区2.0前端开发回顾

<br/>
<br/>
<br/>
<br/>

| 开发时间 | 前端开发者 | 提交次数 |
|--|--|--|
|2019年08月06日-2019年10月31日|索洪波、卢玺、张亚亚、王威、宋伟（特别开发：李彬）| 1381|

<br/>
<br/>
<br/>
<br/>

## 前期准备

  * **技术选型**
    * 物业端（Vue + iview-admin）
    * 业主端（Vue + vant-ui）

  * **制定开发规范**

    * 开发规范地址：[https://shb190802.github.io/front-end-dev-guide/dist/](https://shb190802.github.io/front-end-dev-guide/dist/)
  
  * **使用VSCode**
    * 安装eslint、Prettier、Vetur等插件做代码检测和格式化
    * 配置vscode的setting.json
    * 设置统一的代码格式化风格

  * **代码质量管理**
    * **做好需求分析**
      
      从收到产品提出的需求之后，先做初步整理。然后和相关人员（提出需求的业务、前后端开发者、测试人员等）进行讨论定稿。保证需求、设计、开发统一

    * **使用GIT管理代码**
      1. master、test、dev分支做代码保护，仅支持发起合并请求
      2. 根据teambtion上的任务ID作为分支名，对应开发者
      3. 功能完成之后发起合并
      4. 小步提交

    * **每日代码评审**

      每日下班前，小组抽出30分钟左右对代码进行评审。主要是：
      1. 同步大家工作进度
      2. 一起分析遇到的棘手问题
      3. 对可重复利用方法和功能进行封装
      4. 分享今日心得

    * **编写测试代码**

      由于项目紧急，此项功能没有执行

  * **其他技术选型**
    1. 地图组件（考虑到业主端在微信公众号中使用，地图选择腾讯地图）
    2. 富文本组件（使用quillEditor）
        * markdown编辑器：优势：轻量级；劣势：功能过于简单
        * tinymce ：功能强大的编辑器。前期一直使用；测试时候发现，编译之后尺寸太大。弃用
        * quillEditor： 功能丰富，尺寸适中
    3. 打印组件（lodop，功能强大且支持打印设计）

<br/>
<br/>
<br/>
<br/>

## 开发中

  * **路由懒加载**
  
    为保证首屏加载速度。所有的路由必须使用懒加载

  * **组件设计原则**
    1. 低耦合、高内聚
    2. 公共组件
        * 与业务无关性
        * 要有良好的独立性，单一职责
        * 放在在src/components目录下
        * 组件以zy-开头（例：zy-editor）
    3. 业务组件
        * 就近存放在使用目录下
        * 入口清晰，引用方便
    4. 大驼峰命名（参照开发规范）

  * **安全管理**
    * **通讯数据加密**

      数据加密是模拟https的流程，实现前后的通讯加密处理
      1. 客户端生成RSA的公钥和私钥
      2. 将公钥发给服务端
      3. 服务端生成AES秘钥，使用客户端发送过来的公钥加密后返回给客户端
      4. 之后的前后的通讯都使用AES秘钥进行加解密
      5. 请求体加入时间戳混淆，防止重复提交

    * **权限管理**
    
      对于用户可访问的页面级按钮做到权限管理
      1. 后端在用户登录时候，缓存用户拥有的权限，并将对应权限返回给客户端
      2. 客户端根据用户不同身份和权限，控制对应页面和按钮的显示隐藏
      3. 接口请求期间，带上该按钮对应的按钮ID，服务端二次判断用户是否有该接口的权限
    
    * **预防XSS攻击**
      
      引用第三方xss库对富文本等使用到的文本进行xss过滤

  * **vuex使用**
  
    1. 对应后台消耗较大，前端一次请求基本不变或者变动较少内容，需存入store中（如：常用字典值、省市县信息、物业小区信息等）
    2. 所有存入store中数据的接口请求，必须使用action
    3. 必须使用mapGetters、mapMutations、mapActions
    4. mutation必须全部大写

  * **全局主题配置**

    项目中使用less做css预处理，主色、标题、背景、等主题颜色在设置和引用时候，必须使用全局变量

  * **全局config**

    在config文件中配置一些全局配置。如：
    1. baseUrl
    2. 文件服务器地址
    3. 全局正则校验
    4. 全局错误提示
    
  * **全局utils**
  
    1. 常用util挂在在Vue原型上
    2. 不常用util单独引用
    
  * **合作开发**
  
    1. 代理服务器配置
      
        由于项目前后端属于多人开发，在联调过程中。需要配置不同接口的不同代理服务地址
        * 增加vue.config.local.js文件，并设置git.ignore
        * vue.config.js中引用local文件，并将配置以local为基准合并
        * 个人开发期间使用的相关配置写在vue.config.local.js中
    2. 接口模式

        由于项目牵扯到登录、加解密、权限管理功能模块。前端增加对应的开发模式设置
        1. nocrypt：请求和响应不加解密
        2. nologin：请求和响应不传用户身份用户身份
        3. nofilter：页面不会根据权限过滤菜单和按钮
    
  * **数据mock**
        
      在拿到开发需求之后，前后端需要整理出完成功能所需的api接口。
      前端静态页面开发完成，后端代码短期无法完成时候，前端需要独立mock数据，完成所有逻辑。等待后端开发完之后，直接联调。

    1. 使用koa搭建mock服务器，配合koa-router和koa-body来解析请求地址和请求体
    2. 使用mockjs，模拟返回值
    3. 修改mock服务端response.status，模拟各种异常情况

  * **axios封装**

    由于项目中使用的加解密等各种情况，为了不让每个开发者编写很多重复代码。对axios做了一些封装。

    1. 加解密流程封装在拦截器里
    2. 拦截器中请求体混入时间戳
    3. 封装基础的公共配置（如：header等）

  * **添加loading**

    所有与后端的交互，除非场景要求静默，否则必须加上loading效果

  * **使用filter**

    开发中使用到很多字典值比如：支付方式、性别等。使用全局filter来显示对应文本内容

  * **使用mixin**

    相似的页面会存在大量的共同代码，使用mixin能很好的减少代码量和修改难度


<br/>
<br/>
<br/>
<br/>

## 优化

  开发期间，对整体项目进行了一些优化。

  * **使用webpack-bundle-analyzer**

    使用webpack的webpack-bundle-analyzer插件，对项目所引用的外部组件进行优化。去掉体积很大，但是功能使用不多的第三方库。（tinymce就是再此步骤被优化）

  * **适配多分辨率**

    设计师给出的设计图，移动端为414px、PC端为1920。我们需要将其适配多个尺寸。

    * **移动端**
      
      使用postcss-pxtorem插件，在构建时候，会将px值修改为rem

    * **PC端**

      PC端没有使用pxtorem。使用先放大后缩放的设计。具体思路都是，将body的宽度设置为1920，然后在对根据网页宽度做缩放。
      1. 使用tranfrom
      2. 使用zoom （由于zoom具有更好的兼容性。本项目使用zoom）

  * **图片压缩**

    真的背景等大图片。使用图片压缩。
    1. 使用png-8 替换 png-32/24
    2. 照片类使用jpeg格式并压低质量
    3. 微信端的图片上上传，使用微信图片压缩功能

  * **去掉preload、prefetch**

    preload和prefetch会在浏览器空闲时间，静默下载其他首页未引用静态资源。增加之后页面的访问体验<br />
    但是静默下载默认会消耗服务端带宽，造成服务器出口带宽压力。此处删除

<br/>
<br/>
<br/>
<br/>

## 上线

  * **设置编译模式（env）**

    根据不同的生产环境，设置不同的编译模式，不同的编译模式下可以使用不同的请求方式（如：baseUrl，加解密方式等）
    
  * **Jenkins自动发布**

    配置Jenkins服务器，自动编译上传最新代码
  
  * **开启gzip**

    1. 服务端针对js、css文件开启gzip压缩功能，但是开启压缩功能会导致服务器内向消耗。
    2. 客户端代码在打包时候，针对js、css文件，使用compression-webpack-plugin打包出一份.gz后缀的文件，服务端配置如果存在对应的gz文件。则不再做压缩操作。直接返回gz文件

<br/>
<br/>
<br/>
<br/>

### 开发中的一些小分享

  * **loading的“点点点”效果（如：进行中。。。）**

    经常遇到加载中需要文字后边点点点一直变化的功能。这里使用增加一个类，给类的伪元素加上动画效果。分别在25% 50% 75%处，修改伪元素的content为 。  。。 。。。

  * **大数组合并**

    前端缓存了很多请求数据。如：（楼栋房间信息）。之后的接口请求到每个房间的住户、仪表、账单等信息时候。需要将其合并入房间信息的缓存数据中。<br/>
    此时，可以将住户、仪表、账单等转换为以houseId为主键的Object。然后遍历房间信息合并这个Object
  
  * **数据扁平化**

    因为房间数据是一颗树节点的格式（楼栋-单元-楼层-房间）这样如果要单独修改一个房间的数据。则需要很多次的遍历。<br/>
    此时，将楼栋、单元、楼层、房间分别缓存一个以其ID为主键的Object。直接使用Object[key]来使用

  * **去除生产版本console**

    开发中会大量使用的console来打印日志，业务上线后需要出去掉这些。两个思路：
    1. 开发时候就约定好。将console的方法做好封装。
    2. 上线版本，重写一下console相关方法（本项目使用此方法）
  
  * **使用keepalive.include**

    keepalive在vue开发中，被我们大量使用到。但是为了防止不必要的缓存。我们使用include方法。<br />
    实时监听当前显示的tag标签。将路由的name合并为数组赋值给include。这样保证关闭标签后会释放缓存、切换标签时候保留缓存。<br />
    页面组件的name必须和route的name保持一致
    
  * **浏览器针对input.password默认事件**

    浏览器会根据根对类型为password的input标签，默认选择已保存密码。<br />
    本项目的处理方法。打开页面，但是未输入密码时候，密码类型为text。输入之后，再将类型修改为password

  * **ios返回时候不会刷新页面**
    
    在业主的微信环境下。用户切换到其他页面之后，再返回回来。会导致页面莫名有些功能不可使用。本项目处理方式为。每次返回，重新loading页面。<br />
    监听popstate。在离开页面时候设置标志位。返回回来之后调用reload
    

