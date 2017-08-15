# m99cf

> 99财富wap端网站

## 开发与打包

``` bash
# 安装项目依赖包
npm install

# 启动项目, 可以实现热加载(修改文件实时生效)
npm run dev

# 打包项目
npm run build

```
*******************

## 项目结构
> 首页

    1. 轮播图
    2. 链接:
        A: 平台数据 B: 活动中心 C: 邀请好友 D: 安全保障
    3. 消息中心: 查看公告与个人消息
    4. 体验标与可投标轮播标签展示
        A: 体验标详情页 B: 标的详情页与购买页
    5. 三个推荐标
        
> 投资列表
    
    1. 四种标的展示(下拉刷新与无限加载)
        
> 个人中心

    1. 个人金额信息
    2. 充值
    3. 提现
    4. 投资记录
    5. 回款报告
    6. 红包
    7. 资金明细
    8. 银行卡
        A: 添加银行卡 B: 删除银行卡
    9. 账户设置
        A: 实名认证 B: 绑定托管 C: 修改密码 D: 退出
    10. 资费说明

> 登录/注册/忘记密码

> 模仿安卓中的Webview组件(src/pages/explorer.vue)

    用于显示项目之外的页面(第三方页面iframe)


## 开发技术栈
> 技术类

1. [vue][vue](前端框架, 开发的核心思路是 "首先写好UI界面, 然后使用vue提供的各种工具将数据绑定到视图上, 通过操作数据来改变视图, 尽量减少对DOM的操作.") 
3. [vue-router][vue-router](vue官方提供的路由工具)
1. [vuex][vuex](vue官方提供的状态管理工具, 是一个较高层次的全局作用域, 在项目任何地方都能获取其中的数据, 可以用跨页面的数据传递)
1. [mint-ui][mint-ui](饿了吗出品的ui组件库, 主要使用了这个组件库, 由于使用的较多, 因此在main.js中全局引入了. 如果想要进一步压缩项目体积, 可以在各个页面或组件中单独引入相应的组件.)
1. [vux][vux](另外一套组件库, 主要利用它的轮播图, mint-ui 的swipe组件实在太渣了, 由于是按需导入相关组件, 不用担心整个组件库被打包, 导致项目体积过大.)
3. [fetch-polyfill][fetch](AJAX API; 这是一个比较新的异步请求API, 只有在较新的浏览器上才有, 因此要安装这个包, 使得老旧的浏览器也可以使用这个方法.)
2. [es6][es6](利用es6的新语法, 如箭头函数, 解构, let 等方法能够提高编码的舒适度, 然后利用babel, 将es6语法编译成es5语法, 从而没有兼容性的顾虑.)
4. [pug][pug](html模版引擎, 原名jade, 用缩进来代表DOM元素的嵌套, 主要是为了提高效率, 可以少写很多代码, 项目也显得很简洁, 但是当页面过长, 代码量过多的时候, 要注意缩进)
5. [stylus][stylus](css预处理器, 同上)

> 工具类

2. [vue-cli][vue-cli](vue官方提供的项目脚手架, 集成了webpack, babel等工具, 用于项目的打包和es6编码, 配置好了dev-server, 可以用于项目开发时的热加载以及反向代理等功能.)

>sublime编辑器编辑项目中所需安装的插件

3. 在首选项Packge Control中点击install package安装 vue(height light)、ecmascript、pug、stylus、markdown(preview)

************

## 项目文件结构
1. build
    > 项目开发工具配置

        1. /webpack.base.conf.js
        可以在 alias 中配置开发过程中所需路径的别名. 别名有两个好处, 一方面在开发中能够避免重复输入冗长的路径, 另外一方面可以提高webpack打包的效率.

1. config
    > 项目环境配置

        1. /index.js
        可以在 proxyTable 中配置开发中的 http 反向代理, 将请求代理到某个地址
1. node_modules
    > 项目依赖包
1. src
    > 项目源码

        1. api: 绝大多数的移动端接口都在 apiM.java 中
        2. assets: 静态图片等资源文件夹
        3. components: 组件文件夹, 大部分可复用组件都在其中
        4. config: 
            /mUtils.js: 自己定义的一些工具函数可以放在里面
            /rem.js: 屏幕适配插件
        5. data: 封装了用于 AJAX 的函数 fetchJSON
        6. filters: 封装了一些常用的过滤器
        7. pages: 页面文件夹
        8. routers: 项目路由
        9. store: vuex的文件夹, 用于组件状态管理
        10. style: 与样式相关的文件
        11. App.vue: 根组件
        12. main.js: 全局js, 在里面导入 vue-router, vuex, mint-ui 等文件
1. dist
    > 项目打包的输出文件夹
1. index.html
    > 打包的html源文件, 可以在其中配置一些 meta 信息

## 项目逻辑及实现

1. `App.vue` - 项目的根组件
    >  业务逻辑
        
    项目中所有判断用户已登录的依据是存储在 `localStorage` 中的 `session` , 因此当用户打开这个项目时要判断一下这个 `session` 是否过期.

        if (存在session) {
            请求 "user/status"
                if (session过期) {
                    清除 localStorage 中存储的个人信息
                    跳转到登录界面
                }
        }
    
    >   注意点

    1.这是项目的根组件, 通过该页面子路由的配置, 使得其他的页面组件都是显示在 `router-view` 这个标签中

    2.`keep-alive` 标签中渲染过的组件在切换之后都会保存在内存中, 在下次切换到该页面时就不需要再重新渲染, 从而加快切换速度

    3.`activate`/`deactivated` 代表 `keep-alive` 中组件的 '激活' 和 '离开' 的的生命周期
        
2. `index.vue` - 首页 
     > 业务逻辑

    首页标的显示:

        if (未登录) {
            请求体验标( "experience/fund" )
        } else {
            请求体验标状态( "experience/status" )
                if (没有参与过) {
                    显示体验标
                }
                if (已经从体验标中获取过收益) {
                    请求其他可投标的
                } else {
                    显示体验标      
                }
        }

    首页其他:

        请求轮播图, 推荐标, 新消息     // 消息逻辑请见消息中心

    > 注意点

    修改组件默认样式:

    当我们在使用各种组件的时候, 可能这个组件的样式不能完全达到我们的要求的时候我们需要对样式进行定制
    但是在讲定制之前要说一下 `style` 标签里的 `scoped` 属性, 在 `style` 标签中加入 `scoped` 属性, 可以
    使得这个样式只对当前的组将生效;

    它的原理是 在转化成DOM元素时给其添加一个自定属性,例如: 

        div.test -> <div data-v-2sf8s7f class="test" ></div>

    而写的样式 

        .test { border: 1px solid red; } -> .test[data-v-2sf8s7f] { border: 1px solid red; }

    这样实现了当前组件的样式不会对其他组件产生影响.
    然而当由组件带来的的DOM元素却没有这样的data属性, 因此直接写样式的时候就不会应用到组件上, 解决方法是 在写的样式前面加上 `>>>`, 这样生成的css中就不会添加属性选择器了

3. `invest.vue` - 投资列表
    > 业务逻辑

    功能: 查看四种标的; 下拉加载; 上拉刷新
    分析: 由于四种标的在显示外观上相同的, 而在请求中也只有 `type` 类型的差异, 因此实现思路为四种标的公用同一个列表, 当点击切换标的类型的时候只要切换请求类型, 改变要显示的数据就可以了.
    页面的实现引用了mint-ui组件中的mt-navbar组件,设置一个默认的列表(selected:'4'),每一个列表所现实的content内容不同
    是根据在调完接口后返回的数据来判断每一个content显示的样式,比如通过获得返回的状态来判断每个标是处于复审中,还款中还是已完成等.
    在投资列表中,每一个标都会有自己的startTime,意思是什么时候开始上线投标.所以还要判断与当前时间大小来决定是否显示出来

    进入页面或者点击某个标的, 触发无限加载
            
    切换类型(点击标的按钮触发):

        清空已经当前列表中的数据, 切换 type, 将列表拉到顶部(scrollTop = 0)
            
    无限加载(如果拉到了底部, 或者首次进入就会触发):

            根据 type 请求投资列表
            if (没有更多) return
            if (还没有获取过标的) {
                起始bid为0
            } else {
                起始bid为已得列表的最后一个标的的bid
            }
            根据 type 和 起始bid 请求列表
                if (请求的列表长度 < 10) {
                    显示没有更多
                }
                if ( 起始id == 0 ) {
                    将请求到的列表设为当前列表
                } else {
                    将请求到的列表拼接到当前列表上
                }
             
4. `me.vue` - 个人中心
    > 业务逻辑
        
        显示隐藏资金: 通过过滤器实现, 传递一个参数给过滤器, 通过这个参数判断是返回 "****", 还是原本的值
        

5. `explorer.vue`
    > 业务逻辑

    主要用来显示项目之外的内嵌网页

        切换到该页面时:
            获取url中传递的params.key
            if ( params.key 是数字 ) {
                请求为 活动("global/eventDetail"), 而请求的参数是 key
            } else if ( params.key 不是数字 ) {
                请求不是活动, 就是key 
            }
            根据请求依据发起请求
                将请求到的 html 放到 iframe 中
                将请求到 title 或者 store 中的 title 显示在标题栏
                
6. `msgCenter.vue` - 消息中心
    > 业务逻辑

    消息中心分为公告和通知, 公告(`"blackboard"`)和通知(`"user/notices"`)有些区别

    当客户端向服务器发起通知的请求的时, 当请求到新的通知, 服务器就会判定这些通知已读, 所以我们直接根据请求到的消息中的 `isRead` 判断该通知是否已读就可了;但是服务器对公告是否已读是没有记录, 我们只能在本地记录一下是否已读, 因此当更换浏览器或者清除浏览数据后, 已读的公告又会变成未读的了.

    手机小铃铛

        打开首页时请求是否有新消息

        if ( 本地有公告存储记录 ) {
            读取本地存储的最后一条公告的id 为参数accessID // accessID与是否有新公告有关
        } else {
            accessID 为 0
        }
        根据accessID发起判断是否有新消息的请求("user/noticeCount")
            if ( 有新公告 || 有新通知 ) {
                显示小铃铛的小红点
            }

    页面本身
    
        功能: 切换 公告 和 通知; 下拉刷新; 上拉加载

        下拉刷新, 上拉加载与投资列表一致

        点击已读:
            公告: 读取 localStorage 的记录 pubList, 将当中对应的消息未读状态改为 false, 再写入到 localStorage 中

            通知: 直接去掉消息的红点

            去掉红点的方法: 公告或者通知都是一个子组件("publics.vue"), 它定义了一个 unread 属性, 我们通过改变这个属性来控制小红点的显示与隐藏

7. `bidDetail.vue` - 标的详情
    >业务逻辑

        切换到这个页面时:
            根据url中传递的params.bid
            请求标的详情("fund/detail")
            请求投标者记录("fund/investorList")

        离开这个页面时:
            清除掉该页面中的信息, 以保证下次进入该页面不会有信息残留

        注意点:

            1. 该页面中的项目详情, 相关证明, 服务协议实际上是拿modal框模拟的, 并不是真的跳转到另外一个页面
            2. 最下方的按钮的内容, class, disabled属性是根据标的 status 用 计算属性 返回的, 这里有个坑, 当这个标是预售标的时候, 它的 status 可能是 0 或 1 其中一个, 因此我们要进行更详细的判断
                if ( status == 0 ) {
                    是预售标
                } else if ( status == 1 ) {
                    if ( startTime > 当前时间 ) {
                        是预售标
                    } else {
                        正在销售
                    }
                }
            3. 在使用动态图片地址的时候要用require这个方法
                例如: img( :src="require(`${abc}.png`)")

8. `purchase.vue` - 购买页
    >业务逻辑

    切换到该页面的时候从url中拿到 `bid`
    如果 `url` 有 `query` 信息, 读取 `query.status` 和 `query.message`, 然后给出相应的提示, 然后用 `this.$router.repalce()`, 清除掉 `query` .

    点击选择红包时根据输入的金额 `amount` 和 `bid` 请求可用的红包(`"user/luckyMoney"`).

    点击购买时, 请求(`"fund/transfer"`), 生成订单信息, 然后将得到信息用一个表单将订单信息post到双乾的接口, 在双乾的操作完成后, 又会重新跳回当前页面, 但是会在 `url` 中传输购买成功还是失败的消息.
    
9. `charge.vue` - 充值
    > 业务逻辑

    切换到该页面的时候从url中拿到 `bid`
    如果 `url` 有 `query` 信息, 读取 `query.status` 和 `query.message`, 然后给出相应的提示, 最后用 `this.$router.repalce()`, 清除掉 `query` .

    点击充值的时候, 请求(`"user/recharge"`), 生成订单信息, 然后将得到信息用一个表单将订单信息post到双乾的接口, 在双乾的操作完成后, 又会重新跳回当前页面, 但是会在 `url` 中传输购买成功还是失败的消息. 

    注意点:

    虽然写了选择银行卡, 但是暂且没有用到; 提现手续费暂且不需要    

10. `withDraw.vue` - 提现
    > 业务逻辑

    切换到该页面的时候从url中拿到 `bid`
    如果 `url` 有 `query` 信息, 读取 `query.status` 和 `query.message`, 然后给出相应的提示, 最后用 `this.$router.repalce()`, 清除掉 `query` .
    请求带有可用余额银行卡列表(`"user/bankcard"`, 带上参数 `"action": "withdraw"`)

    当用户输入金额 `500ms` 后, 发起计算手续费的请求(`"user/withdrawFee"`)

    点击提现的时候, 请求(`"user/withdraw"`), 生成订单信息, 然后将得到信息用一个表单将订单信息post到双乾的接口, 在双乾的操作完成后, 又会重新跳回当前页面, 但是会在 `url` 中传输购买成功还是失败的消息. 

11. `investRecord` - 投资记录
    > 业务逻辑

    投资记录和投资列表类似,分为投标中、还款中、和已完成
    页面本身功能: 切换 投标中、还款中、已完成; 下拉刷新; 上拉加载
    
    下拉刷新, 上拉加载与投资列表一致
    在点击每一个列表的二级页面之前,用事先配置好的组件状态管理store文件存储点击的某个列表的content
    由于在store中的index.js中导出了通用的参数(const state ={curInvestRec:{}),在任何一个组件中都可以使用,所以二级页面的显示
    只需把保存的参数在生命周期(activated)中展开即可(this.detail = this.$store.state.curInvestRec)
    由于投标中的二级页面中合同是不予显示的,所以可以通过vue中的v-if指定来判断是否显示。

12. `paymentReport` - 回款预告
    > 业务逻辑

    下拉刷新, 上拉加载与投资列表一致
    回款预告相对来说比较简单,相当于请求过来的数据直接显示在页面上即可,例如请求过来的时间通过预设的过滤器按照年月日来显示,并没有什么逻辑上的东西.

13. `redPacket` - 红包
    > 业务逻辑

    功能: 切换未使用、已使用、已过期; 下拉加载; 上拉刷新

    下拉刷新, 上拉加载与投资列表一致

    分析: 由于红包使用在显示外观上相同的, 而在请求中也只有 type 类型的差异, 因此实现思路为公用同一个列表, 当点击切换标的类型的时候只要切换请求类型, 改变要显示的数据就可以了.
    在对应的componment复用组件中设置props属性加上在红包组件中判断状态来控制显示的效果(:isUse="item.status==8||item.status==6?true:false")
    判断冻结红包也是如此,当用户使用红包但是输入支付密码时候出现错误,红包就会冻结.也就是状态为6的时候设置该红包已冻结即可

14. `financialDetail` - 资金明细
    > 业务逻辑

    功能:和投资记录类似,每一个content都有自己对应的二级详细页面;下拉加载; 上拉刷新

    下拉刷新, 上拉加载与投资列表一致

    分析: 资金明细一级页面的实现和投资记录一样,把请求过来的数据通过循环显示在页面上.只是在二级页面上的做法和投资记录
    有点不同,在一级页面请求数据的时候,把对应的列表内容的index下标和lid唯一标示传入对应的二级页面,然后再次请求和之前一级页面一样的接口,startId即为传过来的唯一标示,pageSize设为1即可.这样即使页面刷新了数据也不会丢失.

15. `addBankCard.vue` - 添加银行卡
    > 业务逻辑

    当切换到该页面的时候

    读取 `localStorage` 中的 `realName`, 这个只能读取, 无法修改. 
    当用户填入卡号, 选择了对应银行和开户行所在地之后, 提交发起请求(`user/bindBankCard`)

    注意点:
    银行名称列表和全国地址列表放在同级目录下的 `bank.js` 和 `location.js` 里面  

16. `userSetting` - 账户设置
    > 业务逻辑

    功能: 用户信息认证、托管的绑定、登录密码的修改、忘记密码、图形验证码、获取验证码、密码重置、退出登录

    分析: 

    用户信息认证:在组件激活(activated)的时候判断是否能获取该用户信息,如果不能获取到直接跳转到登录页面
    否则就把用户的一些信息显示出来.本地存储的esAccount的值是认证绑定托管的条件,绑定托管需要前去开户跳转到
    第三方乾多多平台.点击每一条认证数据时候都会有弹窗出来告知用户是否认证过,实现的方式是引入mint-ui组件中
    的mt-popup组件.

    修改登录密码:用正则表达式的方式来告知用户按规范来设置新的密码,通过增加一个class属性hide和hide的
    属性值hideData来切换用户输入密码是显示还是加密.当用户修改成功的时候点击确认按钮会把用户所有的信息全部清除
    然后前往登录页面登录从而本地存储为最新的用户信息

    忘记密码:忘记密码页面有两个输入框,在进入这个页面之前先请求
    {"action": "user/ resetPasswordCaptcha"}这个接口,判断状态。如果是true的时候就要显示图形验证码然后在进行
    接下来的输入。通过设置正则表达式来让用户按照正确的规范输入,点击获取验证码的时候,通过设置定时器setInterval来改变获取验证码内容为xx秒,当60秒结束后显示重新获取。返回到上一级页面的时候要清空手机号，且把定时器关掉。
    跳到忘记密码页面后重新按照规范修改密码

    退出登录: 清空用户所有信息,跳转到登录页面.

17. `fee` - 资费说明
    > 业务逻辑

    资费说明页面相对来说是最简单的页面,请求对应的接口把数据显示在页面上即可。


18. `login.vue` - 登录
    >业务逻辑

    当输入了用户名和密码, 点击登录请求图形验证码(`user/captcha`), 输入验证码后才发起登录请求(`user/login`), 然后根据返回值给用户相应的提示, 如果成功登录了, 就将请求来的信息保存在 `localStorage` 中.

19. `reg.vue` - 注册
    > 业务逻辑

    当用户输入手机号码后点击获取短信验证码, 会首先弹出图形验证码弹窗, 发起图形验证码请求(`user/registerSMSCaptcha`), 正确输入图形验证码后发起短信验证码请求(`user/registerSMS`), 用户设置密码之后点击注册会发起注册请求(`user/register`)


[vue]: https://cn.vuejs.org/
[vue-router]: https://router.vuejs.org/zh-cn/index.html
[vuex]: https://vuex.vuejs.org/zh-cn/
[mint-ui]: http://mint-ui.github.io/#!/zh-cn
[es6]: http://es6.ruanyifeng.com/
[pug]: https://pugjs.org/zh-cn/api/getting-started.html
[fetch]: https://www.npmjs.com/package/fetch-polyfill
[stylus]: http://sass-lang.com/guide
[vue-cli]: http://vuejs-templates.github.io/webpack/
[vux]: https://github.com/airyland/vux