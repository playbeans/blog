---
layout: post
title:  "京东，阿里，百度，腾讯。人脸识别对比"
date:   2019-07-11 09:31:01 +0800
categories: 人脸识别
tag: 人脸识别
---

* content
{:toc}

### 人脸识别
公司现在做一个人脸打卡的项目，直接使用的是七牛云的接口进行识别，发现识别失败的例子太多，所以应需求对京东，阿里，腾讯，百度的api接口进行调研测试。使用的人脸是公司最近几天到店人脸打卡的数据，所以照片滴没有。

#### 七牛
收费，没有免费额度（公司账号有钱）个人可以找找有没有其他的途径

sdk：Java,Php,Python,Ruby,Node,C#,C/C++.Go,JavaScript

需要调整阈(听产品经理说不是很准)

收费
```
计费示例

某公司2018年5月使用七牛云1:N人脸比对别服务，共发起500万张请求，
则当月使用七牛云1:N人脸比对服务产生的费用为：

确定的结果产生费用：0.19元/百张 * 500万张 = 9500 元

总计费用：9500 元
```

[点击跳转至七牛文档中心](https://developer.qiniu.com/dora/manual/4438/face-recognition)

[点击跳转至七牛SDK中心](https://developer.qiniu.com/kodo/sdk/1239/java)

#### 京东
#####免费的资源

```
有免费的资源，具体没有详细说明 5000 次/日

```
#####外部资源包

1.使用提供的jar包校验(目前只提供了java和python版本)

2.使用guava包
```

import com.google.common.hash.Hashing;
import java.nio.charset.Charset;

String secretKey = "2e148773a0338a8f2200ba90d445f084";
long timestamp = System.currentTimeMillis();
String sign = Hashing.md5().hashString(secretKey + timestamp, Charset.forName("UTF-8")).toString();
```
3.地址
```
https://aidoc.jd.com/user/auth.html
```

##### 图片要求
```
人脸识别的图片要求：
图片格式：jpg/jfif/jpeg、png、bmp
图片像素尺寸：最小 48*48 像素，最大 4096*4096 像素
图片文件大小：小于1.2M

不支持url  需要用base64进行编码
转码的图片 “data:image/jpeg;base64,”需要添加
```
##### 一个demo
```
  private static RequestModel init(String url) {
    RequestModel model = new RequestModel();
    model.setGwUrl(url);
    model.setAppkey(Constants.APP_KEY);
    model.setSecretKey(Constants.SECRET_KEY);
    return model;
  }
  public static String listGroup() {
    RequestModel model = init(Constants.FACE_LIST_GROUP);
    Map<String, Object> queryMap = new HashMap();
    model.setQueryParams(queryMap);
    WxApiCall call = new WxApiCall();
    call.setModel(model);
    return call.request();
  }
```
##### 个人体验

1.需要商家入驻(但是我是个人先看看能不能用)

2.测试人脸查询接口并没有返回应有的数据（主要是人脸搜索接口）返回结果样例如下

```
{"code":"10000","charge":true,"remain":4999,"remainTimes":4999,"remainSeconds":-1,"msg":"查询成功,扣费","result":{"status":0,"message":"1a3b5c8d-c9ed-43b6-87a0-865159cd3534","requestid":"c0f2b54c5ee54d589fa27ae2d7ab2443","result":"{\"faceSearchResults\":[{\"conf\":0.99867636,\"faceID\":{\"faceId\":\"\",\"index\":0,\"outerId\":\"\",\"sim\":0.0},\"height\":425.70337,\"left\":225.33382,\"top\":105.22076,\"width\":297.4229}]}"}}
```
>outerId是用户id，虽然匹配度是0.99，但是不知道是那个用户

3.发现问题找客服，发现没有客服，那就提个工单把，写这个文章的时候已经过了5个工作日了吧，依然没有人鸟我。（放弃调研）

#### 阿里
##### 免费的资源
1.按量预付资源包

优惠活动：API前5000次调用免费，仅限首次购买资源包的用户。

2.按QPS预付费资源包

优惠活动：新用户享受人脸识别全部服务免费6个月试用，试用期每个服务QPS上限为10。

3.按QPS预付费资源包

优惠活动：新用户享受人脸识别全部服务免费6个月试用，试用期每个服务QPS上限为10。

>目前按量计费后付费和预付费资源包的将于2019年7月31日起停止售卖，届时售卖方式将变更为按QPS预付费方式，详细信息见3、按QPS预付费资源包。
##### 外部资源包
发现Java版本的
```
<dependency>
            <groupId>com.aliyun</groupId>
            <artifactId>aliyun-java-sdk-core</artifactId>
            <version>4.1.1</version>
</dependency>
```
##### 图片要求

```
url 或者Base64位的内容
图片格式没有明确要求
图片大小没有明确要求
```

##### 一个demo
```
  static DefaultProfile profile = DefaultProfile
      .getProfile("cn-shanghai", Constants.ACCESS_KEY_ID, Constants.ACCESS_KEY_SECRET);
  static DefaultAcsClient client = new DefaultAcsClient(profile);
  private static CommonRequest init() {
    CommonRequest request = new CommonRequest();
    request.setDomain("face.cn-shanghai.aliyuncs.com");
    request.setVersion("2018-12-03");
    request.setMethod(MethodType.POST);
    return request;
  }
  public static String listGroup() {
    CommonRequest request = init();
    request.setAction("ListGroup");
    CommonResponse response = null;
    try {
      response = client.getCommonResponse(request);
    } catch (ClientException e) {
      e.printStackTrace();
    }
    return response.getData();
  }
```
[阿里文档地址](https://help.aliyun.com/document_detail/118976.html)

##### 个人使用体验

- 有淘宝、支付宝账号就可以注册使用（无需商家入驻）
- 有文档，sdk全面
- 调用的时候一直提示没有权限，[参考地址](https://yq.aliyun.com/articles/704556)
- 没有删除人脸库/人脸组的接口
- 提工单发现还在内测，7.31号才上线，现在是7.11，（花了将近一天的时间得到两个答案，1.还在内测，2.没有删除组/库的接口）（2019年）

> 现在也使用过支付宝的人脸支付，感觉蛮靠谱的。但是公司的需求需要频繁的对一个人脸库/人脸组进行添加和删除。恰恰支付宝没有删除的操作，也不太清楚不删除人脸库/人脸组有什么影响，如果只添加不删除总感觉会有问题。或者人脸组/人脸库有上限。（阿里文档上面没有说）结论：(跟公司业务不符合，放弃)。




#### 腾讯
##### 免费的资源
您每月每种服务均有10000次的免费调用额度
##### 外部资源包
```
https://github.com/TencentCloud/tencentcloud-sdk-java

<dependency>
  <groupId>com.tencentcloudapi</groupId>
  <artifactId>tencentcloud-sdk-java</artifactId>
  <version>3.0.75</version>
</dependency>
```
[腾讯文档](https://cloud.tencent.com/document/product/867/32770)
##### 图片要求
```
支持PNG、JPG、JPEG、BMP，不支持 GIF 图片。
base64编码后的图片数据大小不超过5M。
不编码能不能超过5M还不知道
1个APPID下最多有1000W张人脸。
```
##### 一个demo
```
  private static IaiClient iaiClient;

  private static IaiClient init() {
    if (iaiClient == null) {
      Credential cred = new Credential(Constants.SECRET_ID, Constants.SECRET_KEY);
      ClientProfile clientProfile = new ClientProfile();
      clientProfile.setSignMethod(ClientProfile.SIGN_TC3_256);
      iaiClient = new IaiClient(cred, "ap-guangzhou", clientProfile);
    }
    return iaiClient;
  }
  public static String listGroup() {
    IaiClient client=init();
    GetGroupListRequest request=new GetGroupListRequest();
    GetGroupListResponse response = null;
    try {
      response = client.GetGroupList(request);
    } catch (TencentCloudSDKException e) {
      e.printStackTrace();
    }
    return JacksonUtil.objectToString(response);

  }

```
##### 个人使用体验
- 挺好的
- 有一个比较全面的人脸管理系统，
- 测试的过程中发现人脸给的分偏高，不知道是不是缺点。
- QPS：50还是比较高的（人脸搜索）（每种接口10000，使用完要另外掏钱）
- 腾讯也有活体检测，还没想好怎么用
- 0.0032元/次（后付费模式）每个接口调用超过100w次考虑预付费，可以省100块
- 


#### 百度
听说最近百度各种吹ai，我看看是真吹还是假吹、
##### 免费的资源
- 可以免费使用，自己申请个百度账号就好了，
- 个人的QPS是2，企业实名认证QPS是10
- 人脸搜索默认是5，企业实名认证是10，不支持购买。
##### 外部资源包
java版本的
```
<dependency>
    <groupId>com.baidu.aip</groupId>
    <artifactId>java-sdk</artifactId>
    <version>${version}</version>
</dependency>
```
除了java版本的还有Php，Python，C#,C++,Nodejs
(资源地址)[http://ai.baidu.com/docs#/Face-Java-SDK/top]
##### 图片要求
```
总数据大小应小于10M
图片格式：现支持PNG、JPG、JPEG、BMP，不支持GIF图片
BASE64:图片的base64值，base64编码后的图片数据，编码后的图片大小不超过2M；
```
##### 一个demo
```
  private static AipFace aipFace;

  private static AipFace init() {
    if (aipFace == null) {
      aipFace = new AipFace(Constants.APP_ID, Constants.API_KEY, Constants.SECRET_KEY);
    }
    return aipFace;
  }
    public static String listGroup() {
    AipFace client = init();
    HashMap<String, String> options = new HashMap<String, String>();
    options.put("start", "0");
    options.put("length", "50");
    JSONObject res = client.getGroupList(options);
    return res.toString();
  }
```
##### 个人使用体验
- 百度还有活体检测感觉挺高级的
- 看文档差点劝退，这文档太详细，其实用不到那么东西。
- 80分以上可以判断为同一人，此分值对应万分之一误识率（其实百度给的分挺低的）
- 也有一个比较完善的人脸管理系统（腾讯比百度要多一个搜索报表和人脸报表）
- QPS只有10也不知道够用不，购买也是QPS。（1QPS/300元）[购买地k址](https://console.bce.baidu.com/ai/#/ai/face/overview/index)
- 代码的当可以一直免费用
#### 简单的测试
```
user:004
七牛:0.686066
ali：0.5893581
du:77.953025817871
腾讯：98.15

user:003
七牛：0.671475
ali：0.5940712
du:90.538993835449
腾讯：100.0

user:002
七牛：0.750034
ali：0.769379
du:78.99072265625
腾讯：82.34

user:001
七牛：0.657644
ali：0.52094257
du:75.498382568359
腾讯：60.19

简单的比较看个热闹就行了,没有太大的实际意义(头图片我是不会给的)>_>
```

#### 有浅度的测试
1.七牛，百度，腾讯，数据对比
```
七牛得分    百度得分    腾讯得分
0.825626    0   100
0.762248    56.89342499 90.84
0.661378    90.96895599 98.47
0.733797    92.47420502 100
0.620814    77.75809479 100
0.775964    93.43741608 73.02
0.754578    92.89496613 100
0.758349    92.0373764  73.19
0.685243    91.06843567 96.45
0.837192    93.76371002 100
0.690686    91.62215424 100
0.818003    94.48430634 100
0.681519    92.15245056 100
0.582868    62.80964661 69.17
0.732473    95.28661346 100
0.791208    91.99054718 94.89
0.715457    90.46946716 96.65
0.798079    93.65757751 100
0.838666    94.59299469 100
0.925447    97.20085144 100
0.803739    90.71216583 100
0.824547    0   100
0.88596 95.371521   100
0.859895    96.18724823 100
0.712343    93.25968933 100
0.793286    91.39302826 100
0.882873    94.69506836 100
0.817279    93.39881897 100
0.723683    0   100
0.763036    93.44709778 100
0.785062    92.17715454 75.31
0.843707    94.36878967 100
0.79974 93.17605591 100
0.770389    94.65865326 100
0.828225    94.235466   100
0.55364 54.94102859 53.59
0.884233    54.7767067  100
0.708089    0   92.18
0.867539    94.28856659 100
0.761604    92.4103241  100
0.888701    96.54807281 100
0.835341    92.64788818 100
0.764138    90.5104599  100
0.7221  90.79089355 98.31
0.831828    94.95753479 100
0.783399    94.41584778 100
0.813758    90.66168213 100
0.758998    84.73181915 100
0.781036    93.05545807 100
0.709509    77.09207916 90.47
0.800618    93.54364777 100
0.708012    93.20214844 100
0.914168    95.19690704 100
0.799984    90.4058075  100
0.633477    61.0667305  97.51
0.785098    0   100
0.690084    90.94136047 92.3
0.860058    94.96105957 100
0.706687    72.1044693  61.6
0.680189    53.60061646 81.83
0.694415    91.70696259 95.36
0.799566    0   100
0.658302    76.59204865 98.48
0.6623  91.79598999 81.04
0.696842    82.56327057 90.41
0.767075    93.66599274 100
0.730521    92.04125977 87.78
0.626058    78.3607254  92.63
0.765735    90.31014252 100
0.725041    73.19203186 86.12
0.666285    74.14382935 86.77
0.789751    93.12198639 92.06
0.765903    94.51940155 100
0.834457    95.38706207 100
0.823901    93.2716217  100
0.826538    95.96756744 100
0.777912    91.26364899 100
0.615496    92.51743317 100
0.748429    92.47640228 100
0.712952    89.07553101 100
0.655644    92.74311829 100
0.779686    93.82035828 100
0.845915    91.99180603 100
0.841564    0   100
0.710342    91.68491364 100
0.592489    79.63986969 79.12
0.666325    92.72719574 98.36
0.79454 93.51545715 100
0.692617    79.32109833 89.12
```
> 百度数据有些为零是因为入库照片不合格，所以搜索出来没有结果1.活体检测未通过，2.图片下载失败

#### 有深度的测试
看着不过瘾，对比A场次的打卡的照片和B场次入库的照片进行对比。
```
百度得分    腾讯得分
0   36.6
57.26762772 34.92
50.73818588 44.3
92.47420502 100
0   32.89
0   44.32
53.26260757 31.32
50.63536835 31.42
0   30.45
50.97437286 28.46
0   31.99
60.32827759 49.63
0   37.18
0   44.96
0   41.07
0   37.22
59.79192352 35.05
0   33.75
52.77010727 41.33
0   40.62
90.71216583 100
0   33.32
0   30.27
0   26.09
50.97950745 35.96
0   30.29
0   50.34
0   26.47
0   49.38
0   39.87
0   35.35
0   38.02
0   43.85
0   37.43
94.235466   100
0   30.47
54.7767067  48.85
0   35.55
0   39.14
0   37.25
0   34.04
53.23096466 37.73
0   38.29
90.79089355 98.31
0   32.18
51.62662125 45.13
0   37.14
0   40.42
0   38.28
0   36.89
60.54198074 43.93
0   42.71
0   33.75
0   32.4
0   97.51
0   27.89
0   36.31
94.96105957 100
0   40.79
54.2979126  39.71
53.05374908 38.25
0   32.6
0   53.27
0   52.25
50.41713715 45.75
0   30.32
60.14037704 52.25
0   33.46
0   41.83
0   27.4
64.27716064 55.81
60.22544098 32.1
54.01567078 37.31
0   38.82
0   41.79
0   28.82
50.35219193 31.89
52.92974472 39.54
50.62592316 33.14
55.76007462 40.87
51.51854706 32.49
51.07911301 37.17
0   43.06
0   33.32
0   46.26
60.09726334 37.44
0   31.59
0   33.96
60.48620987 43.32
52.46366501 34.01
0   36.91
0   39.81
0   33.6
0   37.21
0   40.58
0   42.73
0   46.43
0   33.11
0   33.03
0   27.94
55.38132477 39.02
0   39.52
60.58890533 38.69
0   30.49
0   37.54
0   34.26
```
>有些用户可能A和B场次都来了，所以能有一个比较高的结果。挺想看七牛的，但是代码改造有些难度，也不想改造了。百度的标准是80分认为是同一个人。腾讯没有给出标准但是两极分化比较严重，阈值取58-80个人感觉都可以判断是同一个人。
腾讯的管理界面

![GIF](./styles/images/face/tencentConsole.png)


