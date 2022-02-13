# push-wechaty-bot
push-wechaty-bot 是基于 node 与 wechaty 的微信个人号消息推送工具。
# 前提条件
- 微信号有web权限

# push-wechaty-bot与ql对接
- [x] 推送通知
- [x] 添加/更新CK(手动抓取ck发给机器人)
- [x] 查询资产
- [x] 短信登录(对接的Nolan,青龙面板低于2.11.0,Nolan不能用了)
- [x] 支持多容器(config.js填写container相关配置)
- [x] 自动对接青龙一对一通知
  - [x] 京东资产变动通知
  - [x] 京东农场水果成熟通知
  - 需要其他的请在config.js的AutoUpdateQingLong的js_file填写相关配置(如不会填写在下文找到邮箱联系我)

# 更新
```
//如果你目录改了就根据你的目录来
cd /home/push-wechaty-bot

git pull

npm i

//下面config.js可能有更新

//重启项目 index:项目名，执行pm2 list查看name项
pm2 restart index

```
# 推送接口
- 请求地址：http://搭建机器人的服务器ip:端口/api/v1/send
- 请求方式：POST
- Content-Type: application/json
- 参数 
  ```
    {
        "token":"config.js自定义的token",
        "name":"目标联系人的备注",
        "content":"内容"
    }
  ```
# 已知bug
- 机器人自动同意好友申请会占满CPU,是机器人插件问题,已联系作者
# 联系方式
- hxiansen@outlook.com

# 效果展示
<div>
    <img style="width:43%;display:inline-block;" src="https://img30.360buyimg.com/pop/jfs/t1/218863/3/8785/1707784/61c46ca0Ef1e882a8/933a55b36cef3a50.png">
    <img style="width:43%;display:inline-block" src="https://img30.360buyimg.com/pop/jfs/t1/223724/29/1134/887060/61c46cd9Ed668fd05/aa1797d848878136.png">
    <img style="width:43%;display:inline-block" src="https://img30.360buyimg.com/pop/jfs/t1/213651/31/12429/1480848/6209005dE22620c22/1ada447f53e572da.png">
</div>

# 安装
1.环境
- Centos7
- node v16.13.2
- ql面板v2.10.2(非必须,添加/更新CK要ql2.9+,短信登录需要低于2.11.0)

2.安装nodejs (看不懂的可以百度)
- ```
    cd /usr/local && mkdir node && cd node

    yum install wget && wget https://npmmirror.com/mirrors/node/v14.18.2/node-v14.18.2-linux-x64.tar.xz

    tar -xJvf node-v14.18.2-linux-x64.tar.xz
  ```
- 编辑 ~/.bash_profile ⽂件，在⽂件末尾追加如下信息:
    `export PATH=/usr/local/node/node-v16.13.2-linux-x64/bin:$PATH`
- 刷新环境变量，使之⽣效：`source ~/.bash_profile`
- `node -v`和`npm -v`均有版本信息输出即可
- `npm i pm2 -g` 安装pm2来管理进程

3.安装相关依赖和拉取代码
-   ```
    //centos7
    yum install git pango.x86_64 libXcomposite.x86_64 libXcursor.x86_64 libXdamage.x86_64 libXext.x86_64 libXi.x86_64 libXtst.x86_64 cups-libs.x86_64 libXScrnSaver.x86_64 libXrandr.x86_64 GConf2.x86_64 alsa-lib.x86_64 atk.x86_64 gtk3.x86_64 -y

    //ubuntu
    apt-get install  gconf-service libasound2 libatk1.0-0 libatk-bridge2.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget
    ```
- 拉取代码
- `cd /home && git clone https://github.com/hxianseng/push-wechaty-bot.git`
- `cd push-wechaty-bot`
- `npm config set puppeteer_download_host=https://npm.taobao.org/mirrors`
- `npm install puppeteer --unsafe-perm=true --allow-root`
- `npm i`
- 注意看有没有报错
- 在build目录下创建config.js填入👇👇👇,并填写相关配置
    ```
    module.exports = {
    //=======================================必填===================================================
    PORT: 3000,//自定义服务端口
    token: '',//自定义请求api接口的token
    autoAgreeToFriend:false,//是否自动同意好友申请, Ps:有bug,暂时不建议开启
    friendshipMsg:'JD',//发送好友请求的验证消息是 'JD', 机器人会自动通过好友请求
    container: {
        addMode: 1,//cookie添加方式 1 逐个容器添加,添满为止; 2 均衡添加
        containerArr: [
            {
                QLkey: 1, //容器id 从1开始递增
                QLName: '阿里云',//备注
                QLurl: '',//青龙url  http://IP:端口
                clientId: '',//青龙面板=>系统设置=>应用设置 最少权限:环境变量;后续更新可能会用到:配置文件、脚本管理、定时任务
                clientSecret: '',//青龙面板=>系统设置=>应用设置 最少权限:环境变量;后续更新可能会用到:配置文件、脚本管理、定时任务
                cookieNumMax: 30 //cookie容量，最好与诺兰设置为相同数量
            },// 多个容器之间不要忘了 , 号
            /*
            {
                containerId: 2, //容器id 从1开始递增
                QLName: '',
                QLurl: '',
                clientId: '',
                clientSecret: '',
                cookieNumMax: 30 
            },
            ...
             */
        ]
    },
    Nolan: {//诺兰短信登录
        flag: false,//默认关闭
        url: ''//诺兰地址:http://ip:端口  url后面不要加 /login
    },
    AutoUpdateQingLong: { //自动对接青龙 一对一通知
        /**
         * 默认使用的是旁白库
         * 别的库注意以下:
         * 1、库内要有sendNotify.js文件,没有的把青龙自带的sendNotify.js复制过去
         * 2、fileName在库内是否存在,
         * 3、point:切入点 在该js文件内是否唯一
         */
        falg: false,//默认关闭,开启设置为true
        js_library: 'pangbai6_pangbai66',//使用的脚本库名 例如:旁白库的库名为pangbai6_pangbai66
        cron:'0 30 0 * * *',//定时修改青龙的通知文件，防止更新仓库覆盖通知函数 默认每天00:30:00执行(要在更新仓库的后面执行)
        js_file: [//要修改的js文件数组
            {//本{}为一对一通知的主体,不能删除
                fileName: 'sendNotify.js',//文件名 sendNotify.js:脚本库自带的通知脚本,位置：青龙面板-->脚本管理-->shufflewzc_faker3(脚本名称)下的sendNotify.js，不是青龙自带的sendNotify.js
                upArr: [//文件要修改几次这个[]里就有几个{},
                    {
                        point: 'module.exports = {',//切入点:在 fileName 的哪里添加 为空追加到文件最后
                        up_or_down: 'up',//up:向上一行添加 down:向下一行添加
                        remark: '通知主函数',//备注
                        /**下面content中的
                         * PUSH_WECHATY_BOT_TOKEN 的值为 本config.js中token的值
                         * PUSH_WECHATY_BOT_URL 的值为 http://搭建机器人的IP:端口/api/v1/send
                         */
                        content:
                            'let PUSH_WECHATY_BOT_TOKEN = "";\n' +
                            'let PUSH_WECHATY_BOT_URL = "";\n' +
                            '//push-wechaty-bot\n' +
                            'async function wechatyNotify(userName, text, desp, time = 2100) {\n' +
                            '    return new Promise((resolve) => {\n' +
                            '        if (PUSH_WECHATY_BOT_TOKEN) {\n' +
                            '            const body = {\n' +
                            '                token: `${PUSH_WECHATY_BOT_TOKEN}`,\n' +
                            '                name: `${userName}`,\n' +
                            '                content: `${text}\\n${desp}`\n' +
                            '            };\n' +
                            '            const options = {\n' +
                            '                url: PUSH_WECHATY_BOT_URL,\n' +
                            '                body: JSON.stringify(body),\n' +
                            '                headers: {\n' +
                            '                    "Content-Type": "application/json",\n' +
                            '                },\n' +
                            '                timeout,\n' +
                            '            };\n' +
                            '            setTimeout(() => {\n' +
                            '                $.post(options, (err, resp, data) => {\n' +
                            '                    try {\n' +
                            '                        data = JSON.parse(data);\n' +
                            '                        if (data.status == 200) {\n' +
                            '                            console.log(userName + "推送到个人微信:" + data.message)\n' +
                            '                        } else {\n' +
                            '                            console.log(userName + "推送到个人微信:" + data.message);\n' +
                            '                        }\n' +
                            '                    } catch (e) {\n' +
                            '                        $.logErr(e, resp);\n' +
                            '                    } finally {\n' +
                            '                        resolve(data);\n' +
                            '                    }\n' +
                            '                });\n' +
                            '            }, time);\n' +
                            '        }\n' +
                            '        else {\n' +
                            '            resolve();\n' +
                            '        }\n' +
                            '    });\n' +
                            '}'//添加的内容：通知的主函数
                    },
                    {
                        point: 'module.exports = {',//切入点:在 fileName 的哪里添加 Ps:切入点必须在文件中是唯一的
                        up_or_down: 'down',//up:向上一行添加 down:向下一行添加
                        remark: '导出主函数',//备注
                        content: '    wechatyNotify,'//添加的内容：导出这个函数
                    },
                ]

            },
            {//本{}为对接青龙的京东资产变动通知,不需要的可以删除这个{...}
                fileName: 'jd_bean_change.js',//文件名
                upArr: [//文件要修改几次这个[]里就有几个{},
                    {
                        point: '			if (intPerSent > 0) {',//切入点:在 fileName 的哪里添加 为空追加到文件最后 Ps:切入点必须在文件中是唯一的
                        up_or_down: 'up',//up:向上一行添加 down:向下一行添加
                        remark: '京东资产变动通知',
                        //添加的内容 await notify.wechatyNotify(`${$.UserName}`, '通知标题', '消息内容')
                        content: '            await notify.wechatyNotify(`${$.UserName}`, `${$.name}`, `${ReturnMessage}`);'
                    },
                ]

            },
            {//本{}为对接青龙的京东农场水果成熟通知,不需要的可以删除这个{...}
                fileName: 'jd_farm.js',//文件名
                upArr: [//文件要修改几次这个[]里就有几个{},
                    {
                        point: '      if ($.farmInfo.treeState === 2 || $.farmInfo.treeState === 3) {',//切入点:在 fileName 的哪里添加 为空追加到文件最后 Ps:切入点必须在文件中是唯一的
                        up_or_down: 'down',//up:向上一行添加 down:向下一行添加
                        remark: '京东农场水果成熟通知\n',
                        //添加的内容 await notify.wechatyNotify(`${$.UserName}`, '通知标题', '消息内容')
                        content: '        await notify.wechatyNotify(`${$.UserName}`, `京东农场水果成熟通知\\n`, `【京东账号${$.index}】${$.nickName || $.UserName}\\n【提醒⏰】${$.farmInfo.farmUserPro.name}已可领取\\n请去京东APP或微信小程序查看`);'
                    },
                ]

            },
        ]
    },
    //=======================================选填===================================================
    //管理员功能(暂时没有功能)
    admin: {
        isTrue: false,//是否开启
        name: '',//管理员微信昵称
    },
    //系统消息推送，例如：微信登录、退出; 
    pushMessage: {
        choice: 0,//0：不推送 1：Server酱无aqq推送 2：pluspush推送
        pushList: [
            {
                id: 1,
                pushKey: '',//Server酱无aqq推送（暂时只支持ios）
                url: 'https://pushdeer.ftqq.com/message/push'
            }, {
                id: 2,
                pushKey: '',//pluspush推送
                url: 'http://pushplus.hxtrip.com/send'
            }
        ]
    },

    //==========================================================================================
    }

    ```

- 在push-wechaty-bot目录下，用pm2管理进程
- `pm2 start build/index.js`
- `pm2 logs` 可以查看日志输出的微信登录二维码

# 使用
- 添加机器人为好友
- 把ck发给机器人或手动绑定京东id(京东id就是pt_pin=后面的jd-xxx或者jd_xxx)

## 常见问题处理
- ubuntu 下载 puppeteer 失败
  ```
  apt-get install  gconf-service libasound2 libatk1.0-0 libatk-bridge2.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget
  ```
- Failed to download Chromium rxxx 的问题
    ```
    ERROR: Failed to download Chromium r515411! Set "PUPPETEER_SKIP_CHROMIUM_DOWNLOAD" env variable to skip download.{ Error: read ETIMEDOUT at _errnoException (util.js:1041:11) at TLSWrap.onread (net.js:606:25) code: 'ETIMEDOUT', errno: 'ETIMEDOUT', syscall: 'read' }
    ```
    push-wechaty-bot目录下执行

    `npm config set puppeteer_download_host=https://npm.taobao.org/mirrors`

    `sudo npm install puppeteer --unsafe-perm=true --allow-root`


