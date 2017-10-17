epsServerAPI接口说明
====

## 1 创建直播

### 功能介绍
调用本API以创建直播频道
可以通过参数控制以下功能
1.	是否录制
2.	是否自动关闭直播
3.	是否多平台直播
4.	多平台直播的额外用户
可以获得以下关键信息
1.	rtmp推流地址
2.	rtmp播放地址
3.	hls播放地址
### 请求说明
URL :
http://xxx.com/live/create
### 请求方式
GET
### 请求头说明
key	value
content-type	""
### 请求参数说明
名称	类型	是否必须	默认值	备注
token	string	Yes	无	token
isRecord	int	No	1	是否录制：1是 0否
isAutoClose	int	No	0	是否自动关闭(1: 是, 0: 否)
isExtraPull	int	No	0	是否多平台(1: 是, 0: 否)  暂时无用
resolutions	string	No	""	保留参数, 暂时无用
extraCallBackTokens	string	No	""	多平台直播时, 额外的回调用户token， 例如：test，demo
cdn（新增）	string	No	null	 用户可以指定使用那家CDN服务，目前支持两家：七牛和阿里  qiniu or ali
### 请求参数示例
token: "asdfasdf"
isAutoClose: 1
isExtraPull: 1
extraCallBackTokens "sadf"
### 请求示例
http://xxxx.com/live/create?token=1233&isAutoClose=1&isExtraPull=1&extraCallBackTokens=123
### 响应说明
响应类型
JSON
### 响应参数说明
名称	类型	是否必须	默认值	备注
status	int	Yes	0	-1: 失败, 1: 成功
mess	string	Yes	""	成功的结果或者失败的原因
mess参数说明
名称	类型	备注
liveId	int	频道ID
livename	string	流名
server	string	推流域名和节点
name	string	推流queryString
play	string	rtmp直播播放地址
hls	string	hls直播播放地址
pushUrl	sting	推流完整地址
share	string	分享地址(保留参数, 暂时无效)

### 响应示例
正确情况的响应值
{
    "status": 1,
    "mess": {
        "liveId": 1501827836,
        "livename": "test00002163",
        "server": "XXXXXXXX",
        "name": "test00002163?key=08b7ebea74917b13&token=test",
        "play": "rtmp://xxx.com/test2/test00002163",
        "hls": "http://xxxx.com/test2/test00002163.m3u8",
        "pushUrl": "rtmp://xxxxxxx.com/test2/test00002163?key=08b7ebea74917b13&token=test",
        "share": "http://***/test00002163"
    }
}
 
### 错误情况的响应值
{
"status" : 0,
"message" : "token is error"
}

## 2 结束直播

### 接口调用说明
url
/video/change-live-status 
### http请求方式
get方式
### 请求头说明
key	value
content-type	text/html
 
### 入参说明
 
名称	是否必须	参数位置	类型	默认值	备注
token	true	普通参数	String	 	token认证，每个客户有约定好的token值
liveId	true	普通参数	string	 	直播ID
status	true	普通参数	string	 	直播状态 1直播中 2直播结束3禁流
 
### 请求示例
1、测试环境
http://test.xxxx.com/video/change-live-status?liveId=&status=2&token=ejutest
2、生产环境
http://xxxx.com/video/change-live-status?liveId=&status=2&token=ejutest
### 返回参数说明
 
名称	是否必须	参数位置	类型	备注
status	yes	普通参数	int	请求结果状态 1成功 0失败
mess	yes	普通参数	string	请求消息
code	yes	普通参数	string	详细的错误码
data	no	普通参数	String	成功时，该直播流详细数据内容
PS.
红色为新增字段
### 正确返回示例
{
    "status": 1,
    "mess": "update success",
    "data": {
        "id": 11,
        "uid": 1,
        "uname": "管理员",
        "token": "4238a0",
        "livename": "ef7d40",
        "stime": 0,
        "etime": 1500362311,
        "ctime": 1466148751,
        "update_time": 1500362311,
        "ip": "180.167.73.194",
        "status": "2",
        "mess": "1",
        "pushtime": 0,
        "puship": "2",
        "pushserver": "1",
        "playurl": "1",
        "hlsurl": "1",
        "isRecord": 0,
        "isCoding": 0,
        "isHavefile": 0,
        "pushStatus": 0,
        "cdnId": 1,
        "is_auto_close": 0,
        "extraPullStream": 0,
        "resolutionsCommand": "",
        "extraCallBackToken": "",
        "pushUrl": ""
    }
}
### 错误返回示例
{
    "status": 0,
    "code":"1001",
    "mess": "liveId can not be empty",
    "data": null
}
### 错误码
错误代码	错误码说明	http状态	语意
1001	liveId can not be empty	200	参数错误，liveID为空或者未设置
1002	status can not be empty	200	参数错误，status为空或者未设置
1003	can not find the live channel	200	该直播不存在
1004	Can not change live status. Live is already finished.	200	该直播已经结束，不能再修改状态
1005	Only disable or finish status is allowed for the live.	200	已经开始直播的只能修改为禁用和完成
1006	update failed	500	内部错误
1000	token error	200	token错误或者为空

## 3 生成点播

### 功能介绍
此文档专门介绍点播生成后主动回调客户推送点播url及相关信息（所有客户接入此服务，都遵从此格式进行回调）
### 请求说明
请求方式
POST
### 请求头说明
content-type	首播（uid=27 26）	其他
content-type	www-form	form-data
### 请求参数说明
名称	说明	备注
posturl	 回调客户的地址	由客户提供回调地址，配置在ts_user_video_callbackurl表中，并加入token认证，示例：
 
加密规则为： 获取入参中xml_data的值， 该值为xml格式的字符串 + 固定字符串（leju_chat_v0.1）拼接为一个字符串进行md5进行加密
例子：
string
 '<?xml version="1.0" encoding="utf-8"?><xml><status>4</status><liveid>1663435</liveid><livename>test00001630</livename><vid>0</vid><createtime>1501746757</createtime><author>-</author><title>-</title><duration></duration><content></content><keywords></keywords><detail><detail0><startTime>0</startTime><endTime>0</endTime></detail0></detail><videourl><url0>-</url0></videourl></xml>leju_chat_v0.1' 
md5（string）
### 入参	
#### 未推流 未生成点播
{
<status>4</status>  状态固定为4
<liveid>1663788</liveid> liveId
<livename>test00002073</livename>  流名称
<vid>0</vid>
<createtime>1500529065</createtime> uninx时间戳
<author>-</author>
<title>-</title>
<duration></duration>
<content></content>
<keywords></keywords>
<detail><detail0>
<startTime>0</startTime>
<endTime>0</endTime>
</detail0></detail>
<videourl><url0>-</url0></videourl>
}
备注： status=4是表示未推流 没有生成点播， 是和乐居首播约定固定死的状态

#### 有推流 点播生成
{
<update>1</update> //回调次数
<liveid>234837</liveid> //liveId
<status>2</status> //固定状态 2表示成功 4 表示失败
<livename>20170110161955</livename> //流名称
<vid>355655</vid>//点播表的id
<createtime>1494333864</createtime>
<author>ejutest</author>
<title>20170110161955<\/title>
<content></content>
<keywords></keywords>
<detail><detail0>
<startTime>1494333606</startTime>
<endTime>1494333864</endTime>
</detail0></detail>
<videourl><url0>http://videoplay.ejucloud.com\/newcode-EL0000036256--20170509184222.mp4</url0></videourl>//点播地址
}
 
### 返回值	

json字符串	返回jsson字符串，作为记录可以查询回调结果情况

