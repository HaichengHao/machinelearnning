# 人工智能平台使用--语音合成

## 1 百度人工智能平台介绍

百度人工智能平台基于百度自己搜集的大量人机交互数据来学习并生产模型，用户无需训练以其API能够由外部直接进行调用。因此，用户不需要弄懂其中的技术细节就能使用。

![image-20200519120013298](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexmivc3obj31ga0u07ae.jpg)

官网链接：[https://ai.baidu.com/](https://ai.baidu.com/)



## 2 语音合成

### 2.1 介绍

百度语音合成技术能将用户输入的文字，转换成流畅自然的语音输出，并且可以支持语速、音调、音量、音频码率设置，打破传统文字式人机交互的方式，让人机沟通更自然。

![image-20200519141130608](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqbk5t62j31mq0regr6.jpg)



### 2.2 服务开通

1. 选择使用服务

    ![image-20200519141638756](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqgujg2ej31po0nmaw0.jpg)

2. 如果没有登录，会提示登录

    ![登录](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqhfpak6j30n60abtc9.jpg)

3. 进入应用管理后台，这里可以管理应用和创建应用

    ![image-20200519141837524](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqixzz5zj31dg0u0wn6.jpg)

上面的页面也包含了很多有用信息：

- **当前产品提供的服务列表，**比如语音识别-中文普通话、短语音识别-英语等
- **收费状况，**人工智能平台的服务都是有限条件下的免费使用。后面是具体的限制条件。
- **调用量限制，**比如每天可调用的次数。
- **QPS限制，**QPS是Query Per Second的缩写, 指每秒的调用次数限制，是并发的限制数。如果需要高并发，就需要付费了。



4. 创建应用

    ![image-20200519142202720](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqmgvy9sj30u011s0xl.jpg)



5. 获取密钥

    在创建完毕应用后，平台将会分配给应用相关凭证，主要为AppID、API Key、Secret Key。以上三个信息是应用实际开发的主要凭证，每个应用之间各不相同.下图为示例内容

    ![image-20200519142305694](https://tva1.sinaimg.cn/large/007S8ZIlgy1gexqnjnqdqj31ny0dqdi9.jpg)

### 2.3 服务调用

本文介绍语音合成中SDK方法，不过在调用过程中，有几个事情需要注意：

- 合成文本长度必须小于1024字节，如果本文长度较长，可以采用多次请求的方式。切忌文本长度超过限制。
- 语音合成不限制调用量，但是初始的QPS为10，如果默认配额不能满足您的业务需求，请从控制台中申请提高配额。

1. 安装语音合成 Python SDK

    ```
    pip3 install baidu-aip
    ```

2. 新建AipSpeech

```python
from aip import AipSpeech

""" 你的 APPID AK SK """
APP_ID = '你的 App ID'
API_KEY = '你的 Api Key'
SECRET_KEY = '你的 Secret Key'

client = AipSpeech(APP_ID, API_KEY, SECRET_KEY)
```

在上面代码中，常量`APP_ID`在百度云控制台中创建，常量`API_KEY`与`SECRET_KEY`是在创建完毕应用后，系统分配给用户的，均为字符串，用于标识用户，为访问做签名验证，可在AI服务控制台中的**应用列表**中查看。

### 2.4 接口说明

1. 接口描述

    基于该接口，开发者可以轻松的获取语音合成能力

2. 请求说明

    - 合成文本长度必须小于1024字节，如果本文长度较长，可以采用多次请求的方式。文本长度不可超过限制

    举例：要把一段文字合成为语音文件：

```python
result  = client.synthesis('你好百度', 'zh', 1, {
    'vol': 5,
})

# 识别正确返回语音二进制 错误则返回dict 参照下面错误码
if not isinstance(result, dict):
    with open('auido.mp3', 'wb') as f:
        f.write(result)
```

| 参数 | 类型   | 描述                                                         | 是否必须 |
| ---- | ------ | ------------------------------------------------------------ | -------- |
| tex  | String | 合成的文本，使用UTF-8编码， 请注意文本长度必须小于1024字节   | 是       |
| cuid | String | 用户唯一标识，用来区分用户， 填写机器 MAC 地址或 IMEI 码，长度为60以内 | 否       |
| spd  | String | 语速，取值0-9，默认为5中语速                                 | 否       |
| pit  | String | 音调，取值0-9，默认为5中语调                                 | 否       |
| vol  | String | 音量，取值0-15，默认为5中音量                                | 否       |
| per  | String | 发音人选择, 0为女声，1为男声， 3为情感合成-度逍遥，4为情感合成-度丫丫，默认为普通女 | 否       |

3. 返回样例：

```python
// 成功返回二进制文件流
// 失败返回
{
    "err_no":500,
    "err_msg":"notsupport.",
    "sn":"abcdefgh",
    "idx":1
}
```





## 3 语音合成案例

### 3.1 实现步骤

- 实例化AipSpeech
- 调用synthesis方法

### 3.2 代码

- 实例化AipSpeech

```python
""" 你的 APPID AK SK """
APP_ID = '11473655'
API_KEY = 'SGbWA7P4hBXsKWCMt9Gg2ASB'
SECRET_KEY = 'd8kOxB4j08qD9bGRAmYdGCmIPXoNa4xZ'

syn_client = AipSpeech(APP_ID, API_KEY, SECRET_KEY)
```

- 调用合成函数

```python
options = {}
options['vol'] = 5
options['per'] = 4

result  = syn_client.synthesis('在阿根廷3:4法国的比赛中，姆巴佩攻入两球，并制造了法国首打破僵局的点球，他那场犹如天神下凡的表现，惊艳了整个足坛。', 'zh', 1, options)

# 识别正确返回语音二进制 错误则返回dict 参照下面错误码
if not isinstance(result, dict):
    with open('auido.mp3', 'wb') as f:
        f.write(result)
```

