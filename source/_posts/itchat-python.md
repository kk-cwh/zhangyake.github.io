title: 微信自动回复，所有好友头像合成。。。
date: 2017-07-26 11:14:57
tags:
---
![all_friend.gif](http://upload-images.jianshu.io/upload_images/337803-ada279ae9405b7dd.gif?imageMogr2/auto-orient/strip)


基于[itchat](http://itchat.readthedocs.io/zh/latest) 的python微信个人项目 https://github.com/zhangyake/python-itchat
**目前实现功能**

-  图灵机器人自动回复文本信息 msg
-  随机回复图片信息 随机图片放在gif目录中
-  获取所有好友头像 合成为一张大图 
-  获取所有好友基本信息比如昵称 备注 性别 个性签名等 存放在数据库表中
-  下载用户发送的图片 附件 录音 视频文件 （[itchat](http://itchat.readthedocs.io/zh/latest) 存在问题,存在下载失败的情况）

### 步骤

[项目](https://github.com/zhangyake/python-itchat)主要文件是 gif 文件夹 和 weixin.py 下载这两个到本地电脑

1 .  安装[pyhton3]( https://www.python.org/downloads/)最新版 安装时请加入到系统环境变量

2 .  安装一个第三方库——Python Imaging Library 

		pip install Pillow

3 . 安装一个pymysql

		pip install pymysql 

4 . 安装 [itchat](http://itchat.readthedocs.io/zh/latest) 库 

		pip install itchat

5 . 修改 weixin.py 113行key  请自行到[图灵机器人官网](http://www.tuling123.com)申请key

   网址  [http://www.tuling123.com](http://www.tuling123.com)

6 . 修改数据库配置 weixin.py 167行 改为自己的 执行建表语句


```
CREATE TABLE `friends` (
   `id` int(11) NOT NULL AUTO_INCREMENT,
   `NickName` varchar(255) DEFAULT NULL COMMENT '昵称',
   `PYInitial` varchar(255) DEFAULT NULL,
   `PYQuanPin` varchar(255) DEFAULT NULL,
   `RemarkName` varchar(255) DEFAULT NULL COMMENT '备注',
   `RemarkPYInitial` varchar(255) DEFAULT NULL,
   `RemarkPYQuanPin` varchar(255) DEFAULT NULL,
   `Sex` varchar(255) DEFAULT NULL COMMENT '性别 1 男 2 女',
   `Province` varchar(255) DEFAULT NULL COMMENT '省',
   `City` varchar(255) DEFAULT NULL COMMENT '城市',
   `Signature` varchar(255) DEFAULT NULL COMMENT '个人签名',
   PRIMARY KEY (`id`)
 ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;

```

7 .  到当前目录命令行执行 

		python weixin.py
		
   手机微信扫描登录

#### 获取到的用户信息展示

![9A~XW9$_S@IV]N}WM}WXK8X.png](http://upload-images.jianshu.io/upload_images/337803-7bb831c26dab391d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 自动回复信息展示
![image.png](http://upload-images.jianshu.io/upload_images/337803-909745c3652969a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 直接上代码，写的不好 随意喷

```
import os
import time
import requests
import logging
import random
import math
from PIL import Image
import itchat
from itchat.content import *
import pymysql

# 设置自动回复的人字典 valus为 True 为自动回复，False 则不自动回复 ,未添加进来的 也不会自动回复
# key值为好友的昵称的 汉语拼音，如果用户昵称中有其他字符 请登录后 在数据库中查看
autoDict = {'Caroline': True,  'tiankongzhicheng': True}
autoUserNames = {}


# 通过下面的方式进行简单配置输出方式与日志级别
logging.basicConfig(filename='logger.log', level=logging.INFO)

# 注册地图 名片 通知 分享信息 回复方法 
@itchat.msg_register([MAP, CARD, NOTE, SHARING])
def text_reply(msg):
    # print(msg) 这里没有自动回复 对信息进行了日志记录
    if autoUserNames.get(msg['FromUserName']):
        info = time.strftime("%Y-%m-%d %H:%M:%S ", time.localtime()) + \
            autoUserNames[msg['FromUserName']] + " : " + \
            str(msg['Text']) + msg.get('Url', '') + "\n"
        with open('msg.info', 'a', encoding='utf-8') as f:
            f.write(info)
        # logging.info(info) 


# 注册文本信息 回复方法
@itchat.msg_register(TEXT)
def text_reply(msg):
    if autoUserNames.get(msg['FromUserName']):
        info = time.strftime("%Y-%m-%d %H:%M:%S ", time.localtime()) + \
            autoUserNames[msg['FromUserName']] + " : " + \
            str(msg['Text']) + msg.get('Url', '') + "\n"
        #对信息进行了日志记录 写入了文本
        with open('msg.info', 'a', encoding='utf-8') as f:
            f.write(info)
        text = request_robot(info=msg['Text'], userid=msg['FromUserName'])
        itchat.send(text, msg['FromUserName'])


# 在注册时增加isGroupChat=True将判定为群聊回复
@itchat.msg_register(TEXT, isGroupChat=True)
def groupchat_reply(msg):
    if not msg['IsAt'] :
        # 请求图灵机器人 获取要回复的内容
        text = request_robot(info=msg['Text'], userid=msg['FromUserName'])
        # 发送到群里
        itchat.send(text, msg['FromUserName'])


# 在注册 图片 附件 语音 视频 信息 下载方法
@itchat.msg_register([PICTURE, RECORDING, ATTACHMENT, VIDEO])
def download_files(msg):
    msg['Text'](msg['FileName']) #下载文件
    if autoUserNames.get(msg['FromUserName']):
        fileName = os.path.join('gif', '{}.gif'.format(random.randint(1, 25)))
        file = '@{}@{}'.format({'Picture': 'img', 'Video': 'vid'}.get(
            msg['Type'], 'fil'), fileName)
        if msg['Type'] == 'Picture':
            itchat.send(file, msg['FromUserName'])


# 拼接合成好友头像
def make_all_friends_img(image_list, width=120, height=120, save_name='all_friend.jpg'):
    images_count = len(image_list)
    n = int(math.ceil(pow(images_count, 0.5)))
    toImage = Image.new('RGBA', (width * n, height * n), (255, 255, 255))
    for y in range(0, n):
        for x in range(0, n):
            print(x * width, y * height)
            try:
                fromImage = Image.open(image_list.pop())
                fromImage = fromImage.resize((width, height), Image.ANTIALIAS)
                toImage.paste(fromImage, (x * width, y * height))
            except Exception as e:
                print('某头像图片有误')
            if len(image_list) == 0:
                toImage.save(save_name)
                print('合成图像success')
                return


# 获取所以好友头像
def get_all_friends_img(picDir='friends'):
    images = []
    if not os.path.isdir(picDir):
        os.makedirs(picDir)
    for i, friend in enumerate(itchat.get_friends()):
        itchat.get_head_img(userName=friend['UserName'], picDir=os.path.join(picDir, str(i)+'.png'))
        images.append(os.path.join(picDir, str(i)+'.png'))
    return images 

codes_map = {
    100000: True,  # '文本类'
    200000: True,  # '链接类'
    302000: True,  # '新闻类'
    308000: True,  # '菜谱类'
    40001: True,  # '参数 key 错误'
    40002: True,  # '请求内容 info 为空'
    40004: True,  # '当天请求次数已使用完'
    40007: True,  # '数据格式异常'
}


# 向图灵机器人发送请求 获取结果
def request_robot(info='hello', userid='123456', url='http://www.tuling123.com/openapi/api', key='d0ee53f65c46a4206a5b049f1eda674c8'):
    res = requests.post(url, json={'key': key, 'userid': userid, 'info': info})
    if res.status_code == 200:
        data = res.json()
        return response_handle(**data)
    else:
        return '抱歉,稍等。。。'


# 处理机器人返回的数据
def response_handle(**kw):
    code = kw.get('code', 40004)
    res_str = ''
    if code == 100000 or code == 200000:
        res_str = kw.get('text', '') + kw.get('url', '')
    elif code == 302000:
        news = kw.get('list')
        if isinstance(news, Iterable):
            for nw in news:
                res_str += (nw.get('article', '') + '\n' +
                            nw.get('detailurl', '') + '\n')
    elif code == 308000:
        news = kw.get('list')
        if isinstance(news, Iterable):
            for nw in news:
                res_str += (nw.get('name', '') + '\n' +
                            nw.get('detailurl', '') + '\n')
    else:
        res_str = kw.get('text', '')

    return res_str


def get_rooms_info():
    for i, friend in enumerate(itchat.get_chatrooms()):
        logging.info(str(friend))

# 添加用户到数据库 这里(host='192.168.10.10',user='homestead',password='secret',db='test',charset='utf8mb4') 请更改为自己的数据库账户密码
# 建表语句
#  CREATE TABLE `friends` (
#   `id` int(11) NOT NULL AUTO_INCREMENT,
#   `NickName` varchar(255) DEFAULT NULL COMMENT '昵称',
#   `PYInitial` varchar(255) DEFAULT NULL,
#   `PYQuanPin` varchar(255) DEFAULT NULL,
#   `RemarkName` varchar(255) DEFAULT NULL COMMENT '备注',
#   `RemarkPYInitial` varchar(255) DEFAULT NULL,
#   `RemarkPYQuanPin` varchar(255) DEFAULT NULL,
#   `Sex` varchar(255) DEFAULT NULL COMMENT '性别 1 男 2 女',
#   `Province` varchar(255) DEFAULT NULL COMMENT '省',
#   `City` varchar(255) DEFAULT NULL COMMENT '城市',
#   `Signature` varchar(255) DEFAULT NULL COMMENT '个人签名',
#   PRIMARY KEY (`id`)
# ) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;
def add_to_db(**kw):
    connection = pymysql.connect(host='192.168.10.10',user='homestead',password='secret',db='test',charset='utf8mb4')
    try:
        with connection.cursor() as cursor:
            sql = "INSERT INTO `friends` ( `NickName`, `PYInitial`, `PYQuanPin`, `RemarkName`, `RemarkPYInitial`, `RemarkPYQuanPin` , `Sex`, `Province`, `City`, `Signature`) VALUES (%s, %s, %s, %s, %s,%s, %s, %s, %s, %s)"
            cursor.execute(sql,(kw.get('NickName'),kw.get('PYInitial'),kw.get('PYQuanPin'),kw.get('RemarkName','无'),kw.get('RemarkPYInitial'),kw.get('RemarkPYQuanPin'),kw.get('Sex'),kw.get('Province'),kw.get('City'),kw.get('Signature')))
            connection.commit()
    except Exception as e:
        print(e)
    finally:
        connection.close()

def get_auto_friends_username():
    for i, friend in enumerate(itchat.get_friends()):
        if autoDict.get(friend['PYQuanPin']):
            autoUserNames[friend['UserName']] = friend['NickName']
        else:
            pass
            # with open('friends_base.info', 'a', encoding='utf-8') as f:
            #     f.write('昵称:' + friend['NickName'] +  '  备注: ' + (friend['RemarkName'] == '' ? '无': friend['RemarkName']) + ' 性别:' + str(friend['Sex']) + ' 区域: '+ friend['Province'] + friend['City'] + ' 签名:' +friend['Signature'] +  '\n')
        add_to_db(**friend) # 添加用户信息到MySQL数据库friends表中
    print('如下的用户发送text消息，系统将自动回复：')
    for user in autoUserNames.values():
        print('---------------------------------------------')
        print('--------------{}'.format(user))
    images = get_all_friends_img()
    # images.reverse()
    make_all_friends_img(images)
    itchat.send('@img@{}'.format('all_friend.jpg'))#登录时将会把所有好友头像合照发送给登录账户


itchat.auto_login(loginCallback=get_auto_friends_username, enableCmdQR=False)
itchat.run()
```