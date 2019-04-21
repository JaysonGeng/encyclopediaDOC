

# 山大百科 交互文档



[TOC]

| 更新版本号 | 更新人员 | 更新日期   | 更新内容         |
| ---------- | -------- | ---------- | ---------------- |
| v1.0       | 耿继勋   | 2019-04-07 | 初始化           |
| v1.1       | 耿继勋   | 2019-04-11 | 修改init授权接口 |
| v1.2       | 耿继勋   | 2019-04-19 | 修改部分接口     |



### 写在前面

- 基于微信用户绑定，入口为微信网页授权请求，需要回调到一个用于初始化判断用户绑定信息的网页。

当回调到这个地址时，前端会收到由微信以GET方式返回的参数code和state

- 服务器地址

```
刮开查看https://▇▇▇▇▇▇▇▇▇▇▇▇
```

### 初始化

- URL (GET/POST)

```
/user/init
```

- 参数

| 参数名        | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| code          | 表明微信用户身份的凭证，通过之前的回调获得                   |
| signature     | 使用 sha1( rawData + sessionkey ) 得到字符串，用于校验用户信息 |
| encryptedData | 包括敏感数据在内的完整用户信息的加密数据                     |
| rawData       | 不包括敏感信息的原始数据字符串，用于计算签名                 |
| iv            | 加密算法的初始向量                                           |

- 返回

  若用户有绑定过，则返回用户信息

```json
{
    "openId": "",
    "unionId": "",
	"gender": "男",
	"j_password": "",
	"avatar": "",
	"major": "软件工程",
	"name": "耿继勋",
	"nickname": "",
	"studentnumber": "",
	"id": 1,
	"depart": "软件学院",
	"verification": 1
}
```

​	若没有绑定过，则返回

```json
{"status": "unbinding"}
```



- 返回参数说明

| 参数名        | 含义                           |
| ------------- | ------------------------------ |
| openid        | 用户唯一标识                   |
| gender        | 性别                           |
| j_password    | MD5加密后的用户密码            |
| avatar        | 用户头像url                    |
| major         | 专业                           |
| name          | 用户真实姓名                   |
| nickname      | 用户昵称                       |
| studentnumber | 用户学号                       |
| id            | 用户在id                       |
| depart        | 学院                           |
| verification  | 用户权限 0为普通用户 1为管理员 |



### 绑定教务

- URL (GET/POST)

```
/user/binding
```

- 参数

| 参数名     | 含义                                                         |
| ---------- | ------------------------------------------------------------ |
| j_username | 发送方用户ID（用于聊天），通知类信息可以为空                 |
| j_password | 接收方用户ID，该消息将会发送至此用户。如果为空则发送至所有用户 |
| openId     | 用户唯一标识                                                 |

- 返回

  绑定成功则返回用户信息

```json
{
    "openId": "",
    "unionId": "",
	"gender": "男",
	"j_password": "",
	"avatar": "",
	"major": "软件工程",
	"name": "耿继勋",
	"nickname": "",
	"studentnumber": "",
	"id": 1,
	"depart": "软件学院",
	"verification": 1
}
```

​	绑定失败则返回

```json
{ "status": "failed"}
```



### 获取“为你推荐”

​	采用分页加载/局部刷新 模式,每次获取10条推荐

- URL

```
/post/getRecommendTen
```

- 参数

| 参数名     | 含义                                                     |
| ---------- | -------------------------------------------------------- |
| startValue | 起始推荐值，第一次请求为0，之后为最后返回的推荐的value值 |

- 返回 

```json
{
	"code": 0,
	"status": "success",
	"obj": [  //json 数组 ，里面存放返回的10条推荐，此处选择一条作为示例
        ....
        {
		"commentNumber": 62,
		"images": "http://202.194.15.131:8380/isdu/forum/img/1_381124441005183.jpg-",
		"uname": "i山大开发-Jzz",
		"uavatar": "http://202.194.15.132:8380/isdu/avatar/head_1_1554249262054.jpg",
		"collectId": "380-1-",
		"title": "小工具新UI意见征求",
		"uid": "1",
		"likeId": "120-169-",
        "unlikeId": "170-156-",
		"id": 74,
		"time": "1545765533553",
		"tag": "校园",
		"collectNumber": 2,
		"value": 1.274576533553E7,
		"likeNumber": 2,
        "unlikeNumber": 2,
		"info": "目前制作出的效果图：",
        "isSolved": 0 
	},
	....
	....
]
}
```

- 返回参数说明

| 参数名        | 含义                                              |
| ------------- | ------------------------------------------------- |
| commentNumber | 评论数                                            |
| images        | 帖子的图片url， 多个图片以 “-”分隔                |
| uname         | 发帖人昵称                                        |
| uavatar       | 发帖人头像url                                     |
| uid           | 发帖人id                                          |
| collectId     | 收藏该贴的用户id，多个id以“-”分隔                 |
| likeId        | 赞同该贴的用户id，多个id以“-”分隔                 |
| unlikeId      | 不赞同该贴的用户id，多个id以“-”分隔               |
| id            | 帖子id                                            |
| time          | 发布时间，为精切到毫秒数的 时间戳(Unix timestamp) |
| tag           | 分类                                              |
| collectNumber | 被收藏数                                          |
| value         | 推荐值                                            |
| likeNumber    | 赞同数                                            |
| unlikeNumber  | 不赞同数                                          |
| info          | 简略内容                                          |
| title         | 帖子标题                                          |
| isSolved      | 问题是否被解决，0为没有解决，1为已解决            |



### 搜索



```
/post/query
```

- 参数

| 参数名 | 含义         |
| ------ | ------------ |
| key    | 搜索的关键字 |

- 返回

  返回结果格式见   [获取"为你推荐"](#获取"为你推荐")  ，不同的是，**一次返回所有结果**



### 帖子详情获取

- URL

```
/post/detail
```

- 参数

| 参数名 | 含义                    |
| ------ | ----------------------- |
| id     | 帖子id                  |
| openId | 用户id，用户未登录则为0 |

- 返回 


```json
{
	"code": 0,
	"status": "success",
	"obj": {
		"uname": " ", //发帖人id
		"isLike": 0, //是否被当前uid用户赞成，0为未表态，1为赞成，2为不赞成
		"uavatar": " ", //发帖用户头像url
		"collectId": "168-158-", //收藏被问题的用户id，多个id以“-”分隔
		"isCollect": 0, //是否被当前uid用户收藏
		"postId": 106, //帖子id
		"title": "", //帖子标题
		"userId": "3", //发帖人id
                "isSolved": 1, //是否被解决
		"content": "[{\"type\":1,\"content\":\"据说绩点的回传数据变更了，会尽快进行修改，请大家稍安勿躁qwq。。。\\n皮卡丘镇楼\\n\"},{\"type\":0,\"content\":\"http://202.194.15.131:8380/isdu/forum/img/3_36215854568950.jpg\"},{\"type\":1,\"content\":\"\"}]",  //帖子内容，详细说明见后文
		"likeId": "358-169-280-2-246-269-434-400-", //点赞的用户id
		"commentId": "457-458-461-462-463-464-465-", //子评论id，多个id以“-”分隔
		"comment": [{  //评论的具体内容，详细说明见后文
			"children": [],  //该评论的子评论
			"fatherCommenrId": -1, //该评论的父评论id，无父评论则为-1
			"nickname": " ", //评论人昵称
			"commentId": "", //该子评论的子评论id，多个id以“-”分隔
			"id": 457, //该子评论id
			"postId": 106, //回答的帖子id
			"time": "1547652476904",	//回复时间，精确到毫秒的时间戳
			"avatar":  "", //评论的用户头像url
			"userId": 158, //评论的用户id
			"content": "" //评论内容
		}, {
			"children": [],
			"fatherCommenrId": -1,
			"nickname": "洛七",
			"commentId": "",
			"id": 458,
			"postId": 106,
			"time": "1547662619813",
			"avatar": "http://202.194.15.132:8380/isdu/avatar/head_169_1550655539680.jpg",
			"userId": 169,
			"content": "东东nb"
		}, {
			"children": [{
				"fatherCommenrId": 461,
				"nickname": "Grapes",
				"commentId": "",
				"id": 462,
				"postId": 106,
				"time": "1547683592244",
				"avatar": "http://202.194.15.132:8380/isdu/avatar/head_179_1546783635627.jpg",
				"userId": 179,
				"content": "求ln(b)的微分的微分的微分?"
			}, {
				"fatherCommenrId": 461,
				"nickname": "Grapes",
				"commentId": "",
				"id": 463,
				"postId": 106,
				"time": "1547683592458",
				"avatar": "http://202.194.15.132:8380/isdu/avatar/head_179_1546783635627.jpg",
				"userId": 179,
				"content": "求ln(b)的微分的微分的微分?"
			}, {
				"fatherCommenrId": 462,
				"nickname": "洛七",
				"commentId": "",
				"id": 464,
				"postId": 106,
				"time": "1547689198971",
				"avatar": "http://202.194.15.132:8380/isdu/avatar/head_169_1550655539680.jpg",
				"userId": 169,
				"content": "0"
			}],
			"fatherCommenrId": -1,
			"nickname": "i山大开发-菜鸡耿",
			"commentId": "",
			"id": 461,
			"postId": 106,
			"time": "1547665215901",
			"avatar": "http://202.194.15.132:8380/isdu/avatar/head_2_1539599541704.jpg",
			"userId": 2,
			"content": "dddlnb"
		}, {
			"children": [],
			"fatherCommenrId": -1,
			"nickname": "uid_800302024",
			"commentId": "",
			"id": 465,
			"postId": 106,
			"time": "1547725666017",
			"avatar": "http://202.194.15.132:8380/isdu/avatar/6.jpg",
			"userId": 200,
			"content": "冬冬我要给你生猴子"
		}],
		"time": "1547649956389", //发帖时间 
		"tag": "校园" //帖子分类
	}
}
```

- 返回参数说明

见上

- 关于帖子content说明

  为方便帖子内容与图片的保存，评论内容按照顺序，把文字与图片内容分开，其中每一项结构为：

  ```json
  {
  	"type": 1, //类型，表明是文字还是图片内容1为文字，0为图片url
  	"content": ""
  }
  ```

  然后每一项以此放到一个数组中，如：

  ```
  "content": [{
  			"type ": 1,
  			"content": "就可以固体液体看看"
  		},
  		{
  			"type ": 0,
  			"content ": "http: //202.194.15.131:8380/isdu/forum/img/3_36215854568950.jpg"
  		}, {
  			"type": 1,
  			"content": "ghgh"
  		}
  	]
  ```

  

- 关于评论结构的说明

  最外层的comment为包含了所有没有父评论的评论，即顶层评论，存放在一个json数组中，每一个顶层评论都为一个json对象，其中"children": [] ，为该顶层评论的所有子评论json数组，其内容与评论相同



### 我要提问

- URL (POST)

```
/post/uploadFile
```

- 参数

  参照微信 [wx.uploadFile](https://developers.weixin.qq.com/miniprogram/dev/api/wx.uploadFile.html)



### 我要提问

- URL (POST)

```
/post/upload
```

- 参数

| 参数名  | 含义                              |
| ------- | --------------------------------- |
| content | 帖子内容，具体结构如上述的content |
| openId  |                                   |
| title   | 标题                              |
| time    | 时间戳                            |
| tag     | 分类                              |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": null
}
```



### 我要回答/回复评论

- URL (POST)

```
/comment/add
```

- 参数

| 参数名 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| content   | 内容，字符串                           |
| openId | 发起回答请求的用户openId |
| postId    | 被回答的问题的id|
| fatherCommentId    | 若为回复的评论，则需指出被回答的评论的id，若是回答问题，此项为-1|
| time    | 作答时间，精确到毫秒的时间戳|

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "",
	"obj": null
}
```



### 获取分类

- URL 

```
/post/getTag
```

- 参数

  无

  

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": “校园-人文-科技-” //多个分类以“-”分隔
}
```



### 点击分类查看问题

- URL 

```
/post/getPostByTag
```

- 参数

| 参数名   | 含义                              |
| -------- | --------------------------------- |
| tag      | 帖子内容，具体结构如上述的content |
| campus   | 校区                              |
| isSolved | 是否被解决 未解决为0，解决为1     |

- 返回

  返回格式见 搜索

当选择未解决时，问题的返回参数追加 UrgentNumber 属性，即急需解决用户点击数，类型为数字

### 急需解决/点赞/收藏

当用户已经处于点击urgent时，再次请求变取消，当未urgent时，请求为urgent

- URL 

```
/post/urgent
/post/like
/post/collect
```

- 参数

| 参数名 | 含义   |
| ------ | ------ |
| postId | 帖子id |
| openId | 用户id |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": null
}
```



### 判断当前用户是否已经 急需解决/点赞/收藏

- URL 

```
/post/isUrgent
/post/isLike
/post/isCollect
```

- 参数

| 参数名 | 含义   |
| ------ | ------ |
| postId | 帖子id |
| openId | 用户id |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "true",  //此时status为判断是否的属性，值为 true 和 false
	"obj": null
}
```



### 回答的赞/踩

- URL 

```
/comment/like
/comment/unlike
```

- 参数

| 参数名 | 含义       |
| ------ | ---------- |
| openId | 用户openId |
| id     | 回答id     |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": null
}
```



### 判断回答是否被赞/踩

- URL 

```
/comment/isLike
/comment/isUnlike
```

- 参数

| 参数名 | 含义       |
| ------ | ---------- |
| openId | 用户openId |
| id     | 回答id     |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "true",  //此时status为判断是否的属性，值为 true 和 false
	"obj": null
}
```



### 获取所有课程（固定内容，第一请求时直接缓存即可）

- URL 

```
/course/getAll
```

- 参数

  无

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": [	//json 数组里面是每个课程的json对象形式
	…………
		{
			"establishAcademyId": "30",
			"teacherName": "武蕾3",
			"surplus": "-1",
			"idLong": "0303201161",
			"typeName": "专业必修课",
			"type": "2",
			"classTime": "周上:5-3 (软件园5区407d)",
			"teacherId": "200893000014",
			"idShort": "777",
			"name": "高级程序设计语言课程设计(双语)",
			"location": "软件园5区407d",
			"id": 20,
			"credit": "2",
			"establishAcademyName": "软件学院"
		}
	…………
	]
}
```

- course参数说明

| 参数名               | 含义                           |
| -------------------- | ------------------------------ |
| establishAcademyId   | 开课单位id                     |
| establishAcademyName | 开课学院                       |
| teacherId            | 教师id                         |
| teacherName          | 教师                           |
| surplus              | 现容量                         |
| id                   | id                             |
| idLong               | 课程号                         |
| idShort              | 课序号                         |
| name                 | 课程名                         |
| typeName             | 种类名称                       |
| type                 | 种类代码（之后查询请求会用到） |
| classTime            | 具体上课时间地点               |
| location             | 上课地点                       |
| credit               | 学分                           |



### 按分类获取课程（固定内容，第一请求时直接缓存即可）

- URL 

```
/course/getByType
```

- 参数

| 参数名 | 含义   |
| ------ | ------ |
| type   | 类别号 |

- 类别号说明

| 类别号 | 含义     |
| ------ | -------- |
| 11     | 国学     |
| 12     | 创新创业 |
| 13     | 艺术     |
| 14     | 人文     |
| 15     | 社科     |
| 16     | 自然科学 |
| 17     | 工程技术 |
| 18     | 通选课   |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": [	//json 数组里面是每个课程的json对象形式
	…………
		{
			"establishAcademyId": "30",
			"teacherName": "武蕾3",
			"surplus": "-1",
			"idLong": "0303201161",
			"typeName": "专业必修课",
			"type": "2",
			"classTime": "周上:5-3 (软件园5区407d)",
			"teacherId": "200893000014",
			"idShort": "777",
			"name": "高级程序设计语言课程设计(双语)",
			"location": "软件园5区407d",
			"id": 20,
			"credit": "2",
			"establishAcademyName": "软件学院"
		}
	…………
	]
}
```



### 课程详情页

- URL 

```
/course/detail
```

- 测试

  ```
  /course/detail？courseId=20
  ```

- 参数

| 参数名   | 含义     |
| -------- | -------- |
| courseId | 课程的id |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success",
	"obj": {
		"establishAcademyId": "30",
		"teacherName": "武蕾3",
		"surplus": "-1",
		"idLong": "0303201161",
		"typeName": "专业必修课",
		"type": "2",
		"classTime": "周上:5-3 (软件园5区407d)",
		"teacherId": "200893000014",
		"idShort": "777",
		"name": "高级程序设计语言课程设计(双语)",
		"location": "软件园5区407d",
		"comment": [  //评论一项为json数组，里面是每条评论的json对象形式
            …………
            {
			"evaluation": "xxxxx",
			"funLevel": "xxxx",
			"harvest": "xxxx",
			"valuation": "xxxx",
			"attendanceWay": "xxxx",
			"attendanceRate": "xxxx",
			"id": 1,
			"courseId": "20",
			"userId": 1,
			"difficultLevel": "xxxx"
		}
    …………
    ],
		"id": 20,
		"credit": "2",
		"establishAcademyName": "软件学院"
	}
}
```

- comment 参数说明

| 参数名         | 含义         |
| -------------- | ------------ |
| id             | id           |
| courseId       | 课程id       |
| userId         | 评价的用户id |
| attendanceRate | 考勤频率     |
| attendanceWay  | 考勤方式     |
| funLevel       | 趣味程度     |
| difficultLevel | 课程难度     |
| valuation      | 给分情况     |
| harvest        | 课程收获     |
| evaluation     | 补充评价     |



### 添加评价

- URL  **（POST）**

```
/course/comment/add
```

- 参数

| 参数名         | 含义       |
| -------------- | ---------- |
| openId         | 用户openId |
| courseId       | 回答课程id |
| attendanceRate | 考勤频率   |
| attendanceWay  | 考勤方式   |
| funLevel       | 趣味程度   |
| difficultLevel | 课程难度   |
| valuation      | 给分情况   |
| harvest        | 课程收获   |
| evaluation     | 补充评价   |

- 返回(成功)，其他返回异常值则为失败

```json
{
	"code": 0,
	"status": "success", 
	"obj": null
}
```



### 获取我的课程

- URL  

```
/user/getCourse
```

- 参数

| 参数名         | 含义       |
| -------------- | ---------- |
| openId         | 用户openId |


- 返回(成功)，其他返回异常值则为失败

```json
{
  "code": 0,
  "status": "success",
  "obj": [	//json数组
      …………
    {
      "establishAcademyId": "29",
      "teacherName": "王杰3",
      "surplus": "23",
      "idLong": "sd02910650",
      "typeName": "通识必修课程",
      "type": "10",
      "classTime": "2-13周上:6-2(兴隆山体育馆)",
      "teacherId": "200799015559",
      "idShort": "777",
      "name": "体育(3)",
      "location": "兴隆山体育馆",
      "id": 4172,
      "credit": "1",
      "establishAcademyName": "体育学院"
    }
      …………
  ]
}
```

### 获取我的指定类别课程

- URL  

```
/user/getCourseByType
```

- 参数

| 参数名 | 含义             |
| ------ | ---------------- |
| openId | 用户openId       |
| type   | 课程类别，见之前 |

- 返回(成功)，其他返回异常值则为失败

```json
{
  "code": 0,
  "status": "success",
  "obj": [	//json数组
      …………
    {
      "establishAcademyId": "29",
      "teacherName": "王杰3",
      "surplus": "23",
      "idLong": "sd02910650",
      "typeName": "通识必修课程",
      "type": "10",
      "classTime": "2-13周上:6-2(兴隆山体育馆)",
      "teacherId": "200799015559",
      "idShort": "777",
      "name": "体育(3)",
      "location": "兴隆山体育馆",
      "id": 4172,
      "credit": "1",
      "establishAcademyName": "体育学院"
    }
      …………
  ]
}
```

### 获取课程进度条

- URL  

```
/user/getCourseProgress
```

- 参数

| 参数名 | 含义       |
| ------ | ---------- |
| openId | 用户openId |

- 返回(成功)，其他返回异常值则为失败

```
{
  "code": 0,
  "status": "success",
  "obj": [
    {
      "name": "国学",
      "type": "11",
      "credit": "2.0"
    },
    {
      "name": "创新创业",
      "type": "12",
      "credit": "3"
    },
    {
      "name": "艺术",
      "type": "13",
      "credit": "4.0"
    },
    {
      "name": "人文",
      "type": "14",
      "credit": "2.0"
    },
    {
      "name": "社科",
      "type": "15",
      "credit": "3.0"
    },
    {
      "name": "自然科学",
      "type": "16",
      "credit": "2.0"
    },
    {
      "name": "工程技术",
      "type": "17",
      "credit": "3.5"
    },
    {
      "name": "通选",
      "type": "18",
      "credit": "5.5"
    }
  ]
}
```

