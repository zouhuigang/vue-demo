### 新的vue没有dev-server.js文件，如何进行后台数据模拟？
https://segmentfault.com/q/1010000011988039?sort=created
https://blog.csdn.net/qq_34645412/article/details/78833860


参照了别人写的记录一下

最新的vue里dev-server.js被替换成了webpack-dev-conf.js

在模拟后台数据的时候直接在webpack-dev-conf.js文件中修改

第一步，在const portfinder = require(‘portfinder’)后添加

//第一步
const express = require('express')
const app = express()//请求server
var appData = require('../data.json')//加载本地数据文件
var seller = appData.seller//获取对应的本地数据
var goods = appData.goods
var ratings = appData.ratings
var apiRoutes = express.Router()
app.use('/api', apiRoutes)//通过路由请求数据

第二步：找到devServer,在里面加上before（）方法

devServer: {
    clientLogLevel: 'warning',
    historyApiFallback: true,
    hot: true,
    compress: true,
    host: HOST || config.dev.host,
    port: PORT || config.dev.port,
    open: config.dev.autoOpenBrowser,
    overlay: config.dev.errorOverlay
      ? { warnings: false, errors: true }
      : false,
    publicPath: config.dev.assetsPublicPath,
    proxy: config.dev.proxyTable,
    quiet: true, // necessary for FriendlyErrorsPlugin
    watchOptions: {
      poll: config.dev.poll,
    },
    //第二步找到devServer,在里面添加
before(app) {
  app.get('/api/seller', (req, res) => {
    res.json({
      errno: 0,
      data: seller
    })//接口返回json数据，上面配置的数据seller就赋值给data请求后调用
  }),
  app.get('/api/goods', (req, res) => {
    res.json({
      errno: 0,
      data: goods
    })
  }),
  app.get('/api/ratings', (req, res) => {
    res.json({
      errno: 0,
      data: ratings
    })
  })
}
  }


提供一个json.data数据

{
  "seller": {
    "name": "粥品香坊（回龙观）",
    "description": "蜂鸟专送",
    "deliveryTime": 38,
    "score": 4.2,
    "serviceScore": 4.1,
    "foodScore": 4.3,
    "rankRate": 69.2,
    "minPrice": 20,
    "deliveryPrice": 4,
    "ratingCount": 24,
    "sellCount": 90,
    "bulletin": "粥品香坊其烹饪粥料的秘方源于中国千年古法，在融和现代制作工艺，由世界烹饪大师屈浩先生领衔研发。坚守纯天然、0添加的良心品质深得消费者青睐，发展至今成为粥类的引领品牌。是2008年奥运会和2013年园博会指定餐饮服务商。",
    "supports": [
      {
        "type": 0,
        "description": "在线支付满28减5"
      },
      {
        "type": 1,
        "description": "VC无限橙果汁全场8折"
      },
      {
        "type": 2,
        "description": "单人精彩套餐"
      },
      {
        "type": 3,
        "description": "该商家支持发票,请下单写好发票抬头"
      },
      {
        "type": 4,
        "description": "已加入“外卖保”计划,食品安全保障"
      }
    ],
    "avatar": "http://static.galileo.xiaojukeji.com/static/tms/seller_avatar_256px.jpg",
    "pics": [
      "http://fuss10.elemecdn.com/8/71/c5cf5715740998d5040dda6e66abfjpeg.jpeg?imageView2/1/w/180/h/180",
      "http://fuss10.elemecdn.com/b/6c/75bd250e5ba69868f3b1178afbda3jpeg.jpeg?imageView2/1/w/180/h/180",
      "http://fuss10.elemecdn.com/f/96/3d608c5811bc2d902fc9ab9a5baa7jpeg.jpeg?imageView2/1/w/180/h/180",
      "http://fuss10.elemecdn.com/6/ad/779f8620ff49f701cd4c58f6448b6jpeg.jpeg?imageView2/1/w/180/h/180"
    ],
    "infos": [
      "该商家支持发票,请下单写好发票抬头",
      "品类:其他菜系,包子粥店",
      "北京市昌平区回龙观西大街龙观置业大厦底商B座102单元1340",
      "营业时间:10:00-20:30"
    ]
  }}


PS：

所有的修改配置都需要重新启动运行命令的：npm run dev才能生效（很重要，否则无法请求到数据）

https://blog.csdn.net/qq_34645412/article/details/78833860