前端开发，打包后需要搬运代码到服务器端；
这里将介绍一种利用nodejs库实现前端自动部署到服务器的方法：

## 安装依赖
```bash
npm install --save fs-extra
```
```bash
npm install --save scp2
```
```bash
npm install --save ssh2
```

## 也可以在`package.json`指定版本安装，例如：
```bash
"devDependencies": {
  "fs-extra": "^4.0.1",
  "scp2": "^0.5.0",
  "ssh2": "^0.5.5",
  ...
}
```

## 在`package.json`里添加这一行：
```bash
"scripts": {
  "deploy": "node build/deploy.js"
}
```

在`build`文件夹新建一个`deploy.js`文件
```bash
const fs = require('fs-extra')
const path = require('path')
const scp = require('scp2').scp
const config = require('../config/deploy.js')  //在配置文件夹config里放置另一个deploy.js配置文件
const Client = require('ssh2').Client

const program = config.SSH_USER + ':' +
              config.SSH_PASSWORD + '@' +
              config.SSH_PORT + ':' +
              config.SSH_HOST + ':' +
              config.SSH_PATH

const target = path.join(__dirname, '../target')
const dist = path.join(__dirname, '../dist')     //本地打包目录

fs.ensureDirSync(target)
fs.ensureDirSync(dist)

fs.copySync(dist, path.join(target, 'dist/'))     //复制dist下所有的文件和文件夹
//fs.copySync(path.join(__dirname, '../docker-compose.yml'), path.join(target, 'docker-compose.yml')) //docker文件，视情况
//fs.copySync(path.join(__dirname, './nginx.conf'), path.join(target, 'build/nginx.conf'))   //nginx配置文件


console.log('Copying...')  //开始时，提示语句

scp(target, program, (err) => {
  if (err) {
    throw err
    fs.removeSync(target)
  }

  console.log('Copy is done.') //复制完成提示

  let conn = new Client()
  conn.on('ready', function() {
    console.log('Client :: ready')
    conn.exec(config.SSH_CMD, function(err, stream) {
      onExec(err, stream, conn)
    })
  }).connect({
    host: config.SSH_HOST,
    port: config.SSH_PORT,
    username: config.SSH_USER,
    password: config.SSH_PASSWORD
  })
})

function onExec(err, stream, conn) {
  if (err) {
    throw err
    //console.log(err) //提示报错
    fs.removeSync(target)
  }

  stream.on('close', function(code, signal) {
    console.log('Stream :: close :: code: ' + code + ', signal: ' + signal)
    conn.end()
    fs.removeSync(target)
  }).on('data', function(data) {
    console.log('STDOUT: ' + data)
    fs.removeSync(target)
  }).stderr.on('data', function(data) {
    console.log('STDERR: ' + data)
    fs.removeSync(target)
  })
}
```

### 在配置文件夹`config`里放置`deploy.js`配置文件, 没有的话就新建一个config文件夹
```bash
'use strict'

module.exports = {
  SSH_USER: '服务器用户名',   //root
  SSH_PASSWORD: '服务器密码', //root
  SSH_HOST: 'ip或域名',       //192.168.1.1
  SSH_PORT: '22',            //一般为22, 安全可设置为其他端口
  SSH_PATH: '/data/www',      //www
  SSH_CMD: 'cd /data/www && service nginx restart'  //ssh在linux操作命令：定位到/data/www 并且执行nginx重启服务，视情况而定
}
```
## 打包项目：

```bash
npm run build
```

## 部署

```bash
npm run deploy
```

即可自动部署到指定服务器上

## 相关：
https://www.npmjs.com/package/fs-extra

https://www.npmjs.com/package/scp2

https://www.npmjs.com/package/ssh2
