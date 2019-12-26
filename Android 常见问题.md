### 1 如何转人工 
* 相似问题：  
如何转人工？  
转人工失败？  
无法转接人工？  

* 接待模式：  
1 机器人优先：可配置是否显示转人工按钮  
2 人工客服优先：如果客服不在线会自动切到机器人模式  
3 仅人工客服：如果客服不在线，直接结束会话  
4 仅机器人客服：不显示转人工按钮，无法转接人工客服  

当用户排队是，可以指定客户优先接待；可以根据客户以往记录智能选择客服接待。详细配置和说明见：*”设置->在线->在线客服分配“* 

SDK端可设置指定客户排队时优先接入；设置如下：

```
//客服模式控制 -1不控制 按照服务器后台设置的模式运行
//1仅机器人 2仅人工 3机器人优先 4人工优先
info.setInitModeType(-1);

```
智齿SDK分为机器人客服和人工客服两种接入场景，两种场景可自由切换；可通过配置自由选择初始接入模式,转人工逻辑会根据接入模式自动实现。

1、接口配置：由工作台统一管理,*"设置->APP->客服设置(选择你的APP)->客服模式"*  
	关键词配置：*“设置->机器人->转人工关键词设置”*

2、本地配置：

**<font color="#ff0000" > 
注意：本地配置会覆盖接口配置，如果本地设置了大于0的配置，接口配置无效。</font>**


```
【对接指定客服】
    //转接类型(0-可转入其他客服，1-必须转入指定客服)
    info.setTranReceptionistFlag(1);
    //指定客服id
    info.setReceptionistId("your Customer service id");


```

 
 配置关键字转人工，可以省去客户使用时手动点击转人工的操作；智能转人工当机器人无法回复时自动转接人工，可以让客户无感知的情况下切换到人工客服。   

工作台配置：

设置关键词：”设置->机器人->转人工关键词设置“

开启关键词功能：”设置->APP->客服设置（选择APP）->关键词转人工“

智能转人工:”设置->APP->客服设置（选择APP）->开启（机器人引导转人工、连续重复提问转人工、客户情绪负向转人工）“

* SDK 端配置均在Infomation.java中，说明如下：  
**setArtificialIntelligence**
：//默认false：显示转人工按钮。true：智能转人工(值为true时，会在机器人无法回答时显示转人工按钮)。

**setArtificialIntelligenceNum**
：当未知问题或者向导问题显示超过(X)次时，显示转人工按钮。**注意：只有ArtificialIntelligence参数为true时起作用。**  
 
**setTransferKeyWord**:智能转人工关键字配置，关键字作为key,如下; 

``` 
HashSet<String> tmpSet = new HashSet<>();
tmpSet.add("转人工");
tmpSet.add("人工");
info.setTransferKeyWord(tmpSet);
```


### 2 如何设置技能组：
* 相似问题：    
如果获取技能组？    
指定技能组不起作用？    
有客服在线但是提示无客服在线？  
技能组怎么溢出？  

* 技能组  
	客服的分组，一个客服可在多个组，可在工作台设置*"设置->APP->客服设置(选择你的APP)->分组接待"*  是否开启或关闭，如果关闭了配置无效。

1、SDK端指定技能组；如果当前指定技能组没有客服在线，没有设置溢出的情况会提醒暂无客服在线，无法正常转接到其他人工客服。获取技能组信息*"设置->在线->在线技能组设置->获取技能组信息"*：

```

   //预设技能组编号
info.setSkillSetId("your skillCode");
//预设技能组名称，选填
info.setSkillSetName("your skillName");

```  
2、SDK端配置技能组溢出：

* 参数说明：  

```

// 电商版本跨公司溢出逻辑
 //flowType -是否溢出到主商户 0-不溢出 , 1-全部溢出，2-忙碌时溢出，3-不在线时溢出,  默认不溢出
 SobotApi.setFlowType(getApplicationContext(), "1");
 //转人工溢出公司技能组id
 SobotApi.setFlowGroupId(getApplicationContext(), "xxx");
 //设置溢出公司id
 SobotApi.setFlowCompanyId(getApplicationContext(), "xxxxxxx");
    
```
3、SDK端不做任何设置：
显示转人工时，自动弹出技能组列表供用户选项；如果指定了客服或技能组，则不会触发此弹层点击转人工会直接转到指定的人工客服。




### 3 如何确定用户身份
* 相似问题：  
	同一个设备未指定用户id如何区别用户？  
	设置了相同的id，会怎么样？  

partnerid（2.8.0 之前为 userId）： 在线接入时智齿客服系统用于确认用户身份唯一标识，字符串类型无限制，建议设置值。
如果不传 partnerid ，SDK会使用 CFUUIDCreate 方法生成一个编码存在 Keychain 中。    

<font color=#FF0000 >千万不要设置固定的值，这会导致相同的id共享所有的信息(比如把游客都设置为000，后果非常严重，无法修复)。</font>

```
initInfo.setPartnerid("您的用户IDxxx");
```



### 4 如何设置自定义参数
* 相似问题：  
	如何扩展用户信息？   
	怎么传递用户信息到工作台？  
	传递的自定义参数无法统计到？  
	传递的自定义参数工作台无法查看？  

目前SDK自定义参数是通过启动客服页面时统一传递，所以只需要在启动客服页面之前添加到配置信息中即可，没有单独的接口主动触发同步信息。由于这种实现方式的限制，如果已经进入到聊天页面以后修改的自定义信息，是无法实时传递到工作台中。    
自定义参数分为2种类型，分别如下：  
 *不固定key*
 仅在工作台转人工后人工可见，只存在于访客信息中不会同步到客户中心，无法在统计中查看到
 
params(2.8.0以前为customInfo)：map类型，key：value随便填写，最终会转成json传给后端展示。

 ```
 【例】：
  Map<String, String> customInfo = new HashMap<>();
  customInfo.put("技能1", "打麻将");
  info.setParams(customInfo);/* 自定义用户资料 */
 ```
 
 
 *固定key*
 获取key*"设置->自定义字段->客户字段->显示ID"*，人工客服可见(需要把自定义字段添加到工作台中才可以*”设置->自定义字段->工作台字段->配置显示字段“*)，会同步到客户中心，在统计中可以查看到。

customer_fields(2.8.0以前为customerFields)：字典类型，key为显示ID：value随便填写。

 ```
 【例】：
  Map<String, String> customerFields = new HashMap<>();
  customerFields.put("customField1", "2017-05-18");
  info.setCustomerFields(customerFields);
 ```



### 5 推送的实现方式
* 相似问题：  
 收不到推送？
 推送怎么处理？
 推送的未读消息数不对？
 如果关闭智齿消息通道？

注册广播后，当消息通道连通时，可以获取到新接收到的消息。

```
1 注册广播
/**
* action:ZhiChiConstants.sobot_unreadCountBrocast
*/
IntentFilter filter = new IntentFilter();
filter.addAction(ZhiChiConstant.sobot_unreadCountBrocast);
contex.registerReceiver(receiver, filter);
```
```
2 接收新信息和未读消息数
在BroadcastReceiver的onReceive方法中接收信息。
public class MyReceiver extends BroadcastReceiver {
  @Override
  public void onReceive(Context context, Intent intent) {
    //未读消息数
    int noReadNum = intent.getIntExtra("noReadCount", 0);
    //新消息内容
    String content = intent.getStringExtra("content");
    LogUtils.i("未读消息数:" + noReadNum + "   新消息内容:" + content);
  }
}
```
```
当用户不处在聊天界面时，收到客服的消息会将未读消息数保存在本地，如果需要获取本地保存的未读消息数，那么在需要的地方调用该方法即可。如下：
/**
* @param context 上下文对象
* @param uid 用户唯一标识 与information中传的uid一致，
* @return int
*/
SobotApi.getUnreadMsg(Context context,String uid);//如果您没有在information里设置这个uid，请传入null。
    

```


* 开启留言转离线消息功能
	客户在客服无法提供服务时可提交留言，提交的留言可转化为待处理工单，或者以离线消息形式发送给客服。 
需要在后台配置：在 *“设置 -> 留言设置 -> 开启 留言转离线消息”*
开启留言功能后，客户在客服无法提供服务时可提交留言，提交的留言可转化为待处理工单，或者以离线消息形式发送给客服。 




### 6、接⼊入SDK后台挂起时间收到影响
* 相似问题：  
  添加智⻮齿SDK后后台挂起时间变短了了怎么办?
  
  可启动前台服务，保持运行
  
  
  
### 7 通知栏相关问题：
* 相似问题：   
  通知栏、状态栏颜色设置？

``` 
//修改状态栏背景颜色 SobotUIConfig.sobot_statusbar_BgColor = R.color.sobot_white;
```

### 8、如何自定义UI
智齿SDK开放了丰富的可以配置UI选项，所有的选项都在SobotUIConfig.java中配置，详情说明可参加智齿接入文档或直接在SobotUIConfig中查看相关属性注释。如果SobotUIConfig开放的属性无法满足你的需求，你也可以再智齿SDK接入文档最下面下载UI源码自行修改后编译使用。
**[说明：从2.8.0以后，替换图片可以直接在项目中放一个同名图片即可，内部加载会优先使用项目中的图片资源，如果项目中没有会去智齿bundle中加载]**


### 9、留言配置 
* 相似问题：   
	不显示留言？  
	无法跳转留言？
	如果监听留言点击事件？   
 
首先开启留言功能*“设置->在线->留言设置->留言功能”*. 


* 留言转工单 
 	默认留言都是提交一条功能，可添加相对复杂的配置，如图片、文件、自定义字段。
 	
* 转离线消息
	留言后直接给客服发送了一条离线消息，模板相对简单，只能显示普通文本。

如果SDK没有显示留言功能，首先检查当前是否开启了留言功能；如果自定义字段无法显示，判断是否设置了留言转离线消息；如果点击留言没有任何反应，请检查是否设置留言点击监听，拦截了留言点击事件。

SDK相关配置：  
如果想自己实现留言页面，sdk中留言可跳转到自定义页面，如有此需求，可以使用如下方法进行设置：

```
SobotApi.setSobotLeaveMsgListener(new SobotLeaveMsgListener() {
    @Override
    public void onLeaveMsg() {
      ToastUtil.showToast(getApplicationContext(),"在这里实现方法，跳转页面");
   }
});

```

### 10、转人工失败
* 相似问题：   
	无法转接人工？
	总是提示无客服在线？
排查过程：
1、确定当前是否有客服在线    
2、指定了客服ID，所对接的客服不在线 （忙碌、离线、不在上班时间）
3、指定的技能组中没有客服在线 忙碌、离线、不在上班时间）  
4、设置了溢出，但是溢出的技能组中没有客服在线 （忙碌或者离线或者不在上班时间）


### 11、留言记录无法查看
* 相似问题：   
	加载不到留言记录？  
请使用2.8.2及以后版本，第一次留言记录提交由于需要同步用户信息可能有2秒左右的延迟，所以创建完立即查看可能没有数据。



### 12、留言记录有别人的记录
* 相似问题：   
	留言记录串消息？
	
智齿客户中心判断客户的唯一标准包含用户对接ID、电话和邮箱，3者任意一个相同都会合并为同一个用户，当发现留言记录的消息相同是，确定以上信息是否有相同的。


### 13、无法进入留言
* 相似问题：   
	留言点击无效？

1 如果设置了自定义留言跳转页，如果拦截了点击事件，将会点击留言会执行以下代码：

```
SobotApi.setSobotLeaveMsgListener 中的 onLeaveMsg();
```

2 SDK进入留言页面之前会调用接口模板信息，如果调用留言模板接口加载异常也不会进入留言界面，如果不能排除此问题，请联系人工客服解决。


### 14、如何设置工单组
* 相似问题：   
	工单组和技能组有什么区别？   

技能组为在线客服接待技能组，工单组为工单客服的工作组；SDK默认没有工单业务，但是留言转工单就默认添加了一条工单，所以参与到工单业务中。SDK目前无法指定工单组，但是指定接待技能组，会默认同步到工单技能组。
在客服工作台*“设置 -> 工单技能组设置 -> (添加技能组 和 添加组成员)”


### 15、如何显示固定呼叫电话
* 相似问题：   
  怎么设置固定拨打电话号码？	怎么设置结束会话？	怎么显示评价按钮？

聊天右上角 可以显示 关闭，拨号，评价，更多，四种按钮，其中可以配置 拨号，评价，关闭
，三种按钮， 默认一直显示“更多”按钮；拨号，评价
按钮由于位置原因，如果同时开启，拨号按钮优先显示，关闭按钮只能在人工模式下显示，点击关闭会直接离线用户。
从左到右的显示顺序是：关闭、拨号、评价和更多。[[具体参考集成文档](https://www.sobot.com/developerdocs/app_sdk/android.html)]



### 16、初始化和启动页面的作用和区别
* 相似问题：   
  进入SDK就闪退回来？   
  什么调用初始化？  
  可以多次调用初始化吗？


初始化是为了验证客户appkey是否正常，从2.7.2版本开始使用；为了提升启动页面的体验专门分离出来的接口，所以2.7.2以后的版本如果直接调用直接导致后续接口调用失败，返回到上一页面。
一般是在 Applcation
中应用加载时调用初始化，或者在调用启动页面接口之前调用，可根据初始化的返回数据判断是否返回成功再决定是否启动SDK。

```
/**
* 初始化sdk
* @param context 上下文  必填
* @param appkey  用户的appkey  必填 如果是平台版用户需要传总公司的appkey
* @param uid     用户的唯一标识，不能传一样的值，可以为空
*/
SobotApi.initSobotSDK(Context context,String appkey,final String uid);
```

启动SDK页面 启动页面会上传 Infomation的UI相关参数，启动页面接口如下：

```
Information info = new Information();
//appkey 必传
info.setAppkey("Your appkey");
/**
* @param context 上下文对象
* @param information 初始化参数
*/
SobotApi.startSobotChat(context, information);
```



 
### 17、如何配置引导语
* 相似问题：   
	怎么配置机器人引导语？   
	为什么不显示人工欢迎语？   

机器人引导语：*“设置->机器人->机器人信息->常见问题引导”*
开启常见问题引导后，客户将在接入机器人后按顺序收到系统欢迎语及常见问题引导两条消息


机器人欢迎语：*“设置->在线->会话自动应答->APP”*
开启后，客户接入机器人会话，系统将使用此说辞作为欢迎语

人工欢迎语：*“设置->在线->会话自动应答->APP”*
开启后，客户接入人工客服会话，系统将使用此说辞作为欢迎语


### 18、如何配置通告
* 相似问题：   
	通告为啥不可点击？   
	什么是通告？   
	
通告为一条独立的提醒消息，醒目的展示在聊天页面；可配置是否永远置顶在聊天页面的最上面，App端默认置顶显示且显示为单行公告提示，设置方式如下：*“设置->在线->会话自动应答->APP”*；
如果希望通告可以跳转展示更多的内容，请在配置的时候添加相关的跳转链接。



### 19、如何配置自动提醒
* 相似问题：   
	有哪些自动提醒？
	自动提醒不显示？
	提示不显示？
	自动提示不显示？

智齿SDK支持多种提示文案，设置方式如下：*“设置->在线->会话自动应答->APP”*：

* 未知问题说辞
机器人客服无法理解客户的问题时，回复此说辞

* 客服不在线说辞
开启后，人工客服不在线时，系统将回复此说辞

* 客户排队说辞 
开启后，客户转人工咨询遇到排队时，将提示此说辞；如需要在提示中显示排队数，请输入“#人数#”调取，例如您在队伍中的第#人数#个

* 排队超时接入说辞 
开启后，普通客户转人工咨询遇到排队等待超时后，将优先接入，并提示此说辞；在提示中输入“#人数#”显示排队数，输入“#时长#”则显示等待时长

* 客服结束会话说辞 
开启后，客服主动结束会话，将提示此说辞；如需要在提示中显示接待客服名称，请输入“#”调取，例如#客服#结束了本次会话

* 客户超时提示 
开启后，当客户超过设置的时长未回复时，系统将自动给客户推送此说辞

* 客服超时提示 
开启后，当客服超过设置的时长未回复时，系统将自动给客户推送此说辞您还可以设置客服超时转接，点击设置-在线客服分配-超时分配进行客服超时设置

* 客户超时下线 
当客户超过设置的时长未回复时，系统将结束该次会话。 人工接待时，会同步给客户推送此说辞
﻿


### 20、多机器人配置
* 相似问题：   
	如何制定机器人？
	如何开启多机器人？

指定机器人：
根据不同的业务直接指定具体的机器人编号(获取机器人编号*“设置->机器人信息->
robotFlag=xx”*)；设置代码如下：

```
//设置机器人编号
info.setRobotCode("your robot code");

```	


多机器人：
首先在智齿工作台配置多机器人选项*“设置->APP->客服设置(选择创建的应用)->切换接待机器人（开启）”*，开启后在机器人接入是会默认显示切换机器人按钮。



###21、消息撤回
* 相似问题：   
	为什么没有消息撤回功能？
	消息撤回不生效？


消息撤回功能需要在工作台*“设置->在线->客服工作台设置->人工消息撤回”*,开启后，客服可撤回已发送2分钟以内的消息，客户将不能看到已撤回的消息。
当客服撤回消息以后，SDK端会显示客服xxx撤回了一条消息。




###22、未读消息数
* 相似问题：   
	未读消息获取不到？
	未读消息不准确？
	怎么获取未读消息？

智齿SDK的未读消息，目前仅限于人工客服聊天时才有；当用户正在与人工客服沟通离开聊天页面了，SDK的长连接还是会实时获取到客服发送的消息；同时会从客户离开聊天页面开始计算未读消息数量。由于消息数为本地临时存储，所以每次应用kill后重新启动消息数会清零。

在SobotMsgManager.java中如何获取未读消息数

```
     /**
     * 获取未读消息
     *
     * @param appkey
     * @param isClear 是否清除未读计数
     * @param uid     这个uid是用户传来的partnerId
     * @return
     */
    public int getUnreadCount(String appkey, boolean isClear, String uid)
    

清空未读消息：

    /**
     * 清除所有未读消息计数
     *
     * @param uid 这个uid是用户传来的partnerId
     */
    public void clearAllUnreadCount(Context context, String uid) 

```



###23、如何配置询前表单
* 相似问题：   
	如何单独关闭询前表单？
	
询前表单：
询前表单功能用于转人工咨询前的客户信息收集，将收集的信息应用到客服工作台；开启后，客户转人工前必须填写一份表单才能接入人工咨询。


开启询前表单：*“设置->在线->询前表单设置”*，如果仅仅想关闭SDK转人工是的询前表单，在2.8.0以后版本，提供如下配置实现：

```

代码中可单独设置，Information类中设置isCloseInquiryFrom属性，默认false:打开询前表单；
/**
*设置是否关闭询前表单
*/
infomation.setCloseInquiryForm(false)

是否打开询前表单是 && 判断，（sobotQueryFormModel.isOpenFlag() && !isCloseInquiryFrom && sobotQueryFormModel.getField() != null && sobotQueryFormModel.getField().size() > 0）
```



24、如何使用客户服务中心

* 相似问题：   
	什么是客户服务中心？
	怎么进入客户服务中心？
	

客户服务中心：客户在发起咨询前，可看到配置的客户服务中心页，帮助客户快速解决问题，减少人工客服的咨询压力；
开启客户服务中心：*“设置->APP->客服设置->选择创建的应用（设置）->客户服务中心”*
SDK打开客户服务中心页面：

```
    /**
     * 打开帮助中心
     *
     * @param context     上下文对象
     * @param information 接入参数
     */
SobotApi.openSobotHelpCenter(Context,infomation);

```




###25、如何设置文字颜色

* 相似问题：   
	如何设置文字颜色？
	自定义颜色不满足怎么办？
	
	
和更改图片类似，在app 项目下重写color name ，重新赋值。

部分颜色值不需要重写，直接在sobotUIConfig.java 中赋值即可

```
【例】：SobotUIConfig.sobot_chat_left_bgColor=R.color.blue;//设置聊天左气泡背景色

```
【[参考文档](https://www.sobot.com/developerdocs/app_sdk/android.html#_4-4-%E4%BC%9A%E8%AF%9D%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%AE%9A%E4%B9%89ui%E8%AE%BE%E7%BD%AE)】

###26、如何替换相关图片

* 相似问题：
    图片替换
	使用aar集成，如何替换图片？
	
  在app项目中drawable文件夹下添加要替换图片的同名文件，要2倍图和3倍图同时添加。


###27、如果支持多语言
* 相似问题：   
	支持哪些语言？
	没有我要的语言？

智齿Android SDK的多语言文件在value_xx中，目前已包含中文、英文、繁体中文、阿拉伯语，如果要添加更多语言，在app项目中添加相应国家的string.xml文件即可 [参考文档](https://www.sobot.com/developerdocs/app_sdk/android.html)


###28、返回SDK时如何触发评价 
* 相似问题：   
	评价完如何结束会话？   
	返回评价？   
	关闭评价？    

目前SDK支持配置返回时弹出评价和关闭页面弹出评价，详细配置如下：

```
Information info=new Information();
//左上角返回时是否弹出(您是否结束会话？)
info.setShowLeftBackPop(true);
//关闭时是否弹出满意度评价。
info.setShowSatisfaction(false);

// 设置为关闭时，点击关闭是否弹出满意度评价。默认true，弹出，false不弹满意度。直接弹是否关闭的dialog
//关闭时是否弹出满意度评
info.setShowCloseSatisfaction(false);
    
```



29、如何自定义聊天页加号菜单功能按钮
* 相似问题：   
	如何添加+中的按钮？   
	如何添加聊天页加号菜单功能按钮？
	聊天页加号菜单功能按钮？ 
	自定义聊天菜单
	

菜单面板中的功能按钮(点击输入框最右侧+图标显示的按钮)，目前只支持扩展不支持删除。默认根据配置机器人会有“留言”、“评价”；人工会有：“图片”、“拍摄”、“文件）”、“留言”、“评价”；扩展配置如下：

```
【例子:定位功能】
         final String ACTION_LOCATION = "sobot_action_location";
        //位置
        ChattingPanelUploadView.SobotPlusEntity locationEntity = new ChattingPanelUploadView.SobotPlusEntity(ResourceUtils.getDrawableId(getApplicationContext(), "sobot_location_btn_selector"), ResourceUtils.getResString(getApplicationContext(), "sobot_location"), ACTION_LOCATION);
        List<ChattingPanelUploadView.SobotPlusEntity> tmpList = new ArrayList<>();
        tmpList.add(locationEntity);
        //添加赋值菜单
        SobotUIConfig.pulsMenu.operatorMenus = tmpList;
        //sSobotPlusMenuListener 带单点击监听，只能有一个，否则，下边的会覆盖上边的
       SobotUIConfig.pulsMenu.sSobotPlusMenuListener = new SobotPlusMenuListener() {
            @Override
            public void onClick(View view, String action) {
                if (ACTION_LOCATION.equals(action)) {
                    Context context = view.getContext();
                    //在地图定位页面获取位置信息后发送给客服：
                    SobotLocationModel locationData = new SobotLocationModel();
                    //地图快照，必须传入本地图片地址，注意：如果不传会显示默认的地图图片
                    locationData.setSnapshot(Environment.getExternalStorageDirectory().getAbsolutePath() + "/1.png");
                    //纬度
                    locationData.setLat("40.057406655722");
                    //经度
                    locationData.setLng("116.2964407172");
                    //标点名称
                    locationData.setLocalName("金码大厦");
                    //标点地址
                    locationData.setLocalLabel("北京市海淀区六道口金码大厦");
                    SobotApi.sendLocation(context, locationData);

                }
            }
        };

   SobotUIConfig.pulsMenu.operatorMenus//人工菜单
   SobotUIConfig.pulsMenu.menus//机器人菜单


```


###30、添加标签链接
* 相似问题：   
	什么是标签链接？   
	标签链接怎么监听？   

标签链接(*"设置->APP->客服设置->添加标签链接"*)，开启后在咨询端输入框上方出现设置的标签，客户可以点击标签快速查看对应内容；
SDK所有链接监听，监听方法如下：

```
【例】： 方法返回true,表示拦截
                        SobotApi.setNewHyperlinkListener(new NewHyperlinkListener() {
                            @Override
                            public boolean onUrlClick(String url) {
                                if (url.contains("551329")) {
                                    ToastUtil.showToast(getApplicationContext(), "点击了超链接，url=" + url);
                                    //do().....
                                    return true;
                                }
                                return false;
                            }

                            @Override
                            public boolean onEmailClick(String email) {
                                ToastUtil.showToast(getApplicationContext(), "点击了邮件，email=" + email);
                                return false;
                            }

                            @Override
                            public boolean onPhoneClick(String phone) {
                                ToastUtil.showToast(getApplicationContext(), "点击了电话，phone=" + phone);
                                return false;
                            }
                        });
```






###31、2.7.14和2.8.2有什么区别
* 相似问题：   
	为什么要升级最新版本？   
	旧版本还可以使用吗？

智齿对已经发布的SDK版本始终能提供接口服务，但是由于系统升级和外部环境变化，老版本SDK无法始终支持新的手机软件环境，新版本会随时适配最新的技术变更，所以最好能使用最新的SDK版本。

由于在2.8.0之后的版本做了UI重构和对接参数统一规范，在2.8.0之后的版本会使用新的UI和参数名称；对于已经使用的用户不管从体验和对接都有很大变化，所以现在SDK会有2个版本同时发布维护，后期会合并为一个版本。

* 主要不同：
1 UI风格改版，比如会话气泡、各种图标、颜色等；  
2 顶部标题变化，默认只显示头像，没有文字标题(可配置显示)；  
3 聊天框不在显示头像和名称；  


###32、如何设置录音开关，实现的流程是什么
* 相似问题：   
	如何关闭机器人语音？     
	机器人语音无法识别？     
	如何关闭人工语音？    
	语音有哪些限制？     

语音功能包含语音的播放和录音发送功能，所以需要用到系统的麦克风权限，否则录音功能无法正常使用。

* 机器人语音
	机器人语音需要单独付费购买,在统计质检中会有对比翻译，如果没有购买机器人语音，所有的机器人语音将无法正常返回结果；如果确认没有购买机器语音建议关闭机器人语音功能，代码配置如下：
	
```
//付费功能，否则语音无法识别
info.setUseRobotVoice(false);

```
	
* 人工语音
	人工语音是默认可用功能，只要是人工接待状态均可发送语音；发送的语音客服可以直接收听。如果不想让人工看到语音文件，可以在SDK端配置关闭语言功能，SDK端将不再显示发送语音按钮，代码配置如下：
	
```
//是否使用语音功能
info.setUseVoice(false);

```
		
 

###33、如何使用地理位置信息
* 相似问题：   
	怎么发送位置？        
	如何获取坐标？       
	会不会影响系统审核？    

智齿SDK支持客户发送地理位置类型消息，客户可通过SDK实现消息展示和点击监听；由于地理位置需要敏感权限信息，智齿不会主动获取地址位置信息，使用客户定位插件不一样，智齿希望灵活使用客户的定位方式和选择位置功能，所以需要自行传递位置到SDK以及打开显示。


配置发送按钮，添加发送按钮后转接到人工后会自动显示在键盘区域，代码如下：

```
【参考：自定义聊天页菜单】

```



###34、如何使用商品或订单卡片信息
* 相似问题：   
	如何发送商品信息？        
	如何自动发送商品信息？       
	自动发送订单信息？          
	发送订单信息？    
	发送商品信息？  
	
* 自动发送
	SDK支持配置自动发送商品和订单信息，在人工成功接待以后主动发送一条配置信息给人工客服，代码实现如下：
	
```
   ConsultingContent consultingContent = new ConsultingContent(); 
   consultingContent.setSobotGoodsTitle("标题"); //标题
   consultingContent.setSobotGoodsImgUrl("商品图片链接"); //内容图片 选填
   consultingContent.setSobotGoodsFromUrl("商品链接"); //发送内容
   consultingContent.setSobotGoodsDescribe("商品描述");
   consultingContent.setSobotGoodsLable("￥2150");//标签，一般为价格
   consultingContent.setAutoSend(true);//转人工时，是否自动发送
                        
   info.setConsultingContent(consultingContent);//设置到信息当中



// 配置订单信息
 List<OrderCardContentModel.Goods> goodsList = new ArrayList<>();
 goodsList.add(new OrderCardContentModel.Goods("苹果", "https://img.sobot.com/chatres/66a522ea3ef944a98af45bac09220861/msg/20190930/7d938872592345caa77eb261b4581509.png"));
 goodsList.add(new OrderCardContentModel.Goods("苹果1111111", "https://img.sobot.com/chatres/66a522ea3ef944a98af45bac09220861/msg/20190930/7d938872592345caa77eb261b4581509.png"));
 goodsList.add(new OrderCardContentModel.Goods("苹果2222", "https://img.sobot.com/chatres/66a522ea3ef944a98af45bac09220861/msg/20190930/7d938872592345caa77eb261b4581509.png"));
 goodsList.add(new OrderCardContentModel.Goods("苹果33333333", "https://img.sobot.com/chatres/66a522ea3ef944a98af45bac09220861/msg/20190930/7d938872592345caa77eb261b4581509.png"));
 OrderCardContentModel orderCardContent = new OrderCardContentModel();
 //订单编号（必填）
 orderCardContent.setOrderCode("zc32525235425");
 //订单状态
 orderCardContent.setOrderStatus(1);
 //订单总金额
 orderCardContent.setTotalFee(1234);
 //订单商品总数
 orderCardContent.setGoodsCount("4");
 //订单链接
 orderCardContent.setOrderUrl("https://item.jd.com/1765513297.html");
 //订单创建时间
 orderCardContent.setCreateTime(System.currentTimeMillis() + "");
 //转人工时，是否自动发送
 orderCardContent.setAutoSend(false);
 //订单商品集合
 orderCardContent.setGoods(goodsList);
 //订单卡片内容
 info.setOrderGoodsInfo(orderCardContent);


```
	
	
* 手动发送
由于商品信息和订单信息只有人工客服才能识别，所以目前仅能对人工发送；通过配置发送按钮，在人工状态下会自动显示，监听点击事件可以实时改变发送的内容.


// 配置发送按钮
【[参考：自定义聊天菜单](https://www.sobot.com/developerdocs/app_sdk/android.html#_4-4-%E4%BC%9A%E8%AF%9D%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%AE%9A%E4%B9%89ui%E8%AE%BE%E7%BD%AE)】

	

###35 aar 集成问题
* 相似问题：   
	无法搜索到当前版本？      
	搜索到当前版本但是有缓存?      
	如何缓存清理?   
	pod版本如何替换图片?    
	
智齿的aar版本目前只提供带UI版本，如下表；如果使用aar需要更换图片资源，可以放一个同名图片资源在项目中即可，会优先加载项目中的图片。 

|   |普通版|电商版|
|---|---|---|
|Android|sobotsdk|sobotsdk-mall
|Androidx|sobotsdk_x|sobotsdk-mall_x|

如：引用 compile 'com.sobot.chat:sobotsdk:2.8.2'


清理缓存

```
使用AndroidStudio->File->Invalidate Caches/Restart...

```




###36、APICloud版本何时更新
* 相似问题：   
	APICloud？   
	
APICloud版本需要对方审核，目前审核周期为一周；鉴于APICloud目前版本发布周期比较受限于审核时间，智齿SDK会上传目前较为稳定的版本，所以APICloud版本会比目前官网上的版本更新的慢，如果发现问题或需要使用新的功能，可联系我们技术评估上传。 



###37、什么是离线消息
* 相似问题：   
	离线消息？
	收不到消息?    

Android SDK
代码相关使用说明：

```
开启离线消息
// 开启通道接受离线消息，开启后会将消息以广播的形式发送过来,如果无需此功能那么可以不做调用
SobotApi.initSobotChannel(getApplicationContext(), "uid");

关闭离线消息
// 关闭通道,清除当前会话缓存
SobotApi.disSobotChannel(getApplicationContext());
```
注册广播后，当消息通道连通时，可以获取到新接收到的消息。

```
1 注册广播
/**
* action:ZhiChiConstants.sobot_unreadCountBrocast
*/
IntentFilter filter = new IntentFilter();
filter.addAction(ZhiChiConstant.sobot_unreadCountBrocast);
contex.registerReceiver(receiver, filter);
```
```
2 接收新信息和未读消息数
在BroadcastReceiver的onReceive方法中接收信息。
public class MyReceiver extends BroadcastReceiver {
  @Override
  public void onReceive(Context context, Intent intent) {
    //未读消息数
    int noReadNum = intent.getIntExtra("noReadCount", 0);
    //新消息内容
    String content = intent.getStringExtra("content");
    LogUtils.i("未读消息数:" + noReadNum + "   新消息内容:" + content);
  }
}
```
```
当用户不处在聊天界面时，收到客服的消息会将未读消息数保存在本地，如果需要获取本地保存的未读消息数，那么在需要的地方调用该方法即可。如下：
/**
* @param context 上下文对象
* @param uid 用户唯一标识 与information中传的uid一致，
* @return int
*/
SobotApi.getUnreadMsg(Context context,String uid);//如果您没有在information里设置这个uid，请传入null。
    

```


###38、如何离线用户
* 相似问题：    
  离线用户？    
  结束会话？    
  

根据客户使用场景，有时需要直接结束当前会话以减少排队和人工资源占用，可通过调用接口直接结束当前会话，离线用户。配置如下：

```
// 如果离线用户后，希望收到离线消息，可以参考离线消息配置

/**
 退出智齿客户 移除推送
 说明：调用此方法后将不能接收到离线消息，除非再次进入智齿SDK重新激活,
 isClosePush:YES ,是关闭push；NO离线用户，但是可以收到push推送
* @param context 上下文对象
*/
SobotApi.exitSobotChat(context);;


```


###39、如何自定义顶部标题 
* 相似问题：    
	自定义标题？    
	不显示名称？   
	 

```
/**
 * 设置聊天界面头部标题栏的昵称模式
 *
 * @param context      上下文对象
 * @param title_type   titile的显示模式
 *    SobotChatTitleDisplayMode.Default:显示客服昵称(默认)
 *    SobotChatTitleDisplayMode.ShowFixedText:显示固定文本
 *    SobotChatTitleDisplayMode.ShowCompanyName:显示console设置的企业名称
 * @param custom_title 如果需要显示固定文本，需要传入此参数，其他模式可以不传
 * @param isShowTitle  是否显示标题
 */
SobotApi.setChatTitleDisplayMode(getApplicationContext(),
        SobotChatTitleDisplayMode.ShowFixedText, "昵称", true);  


/**
 * 设置聊天界面标题栏的头像模式
 *
 * @param context           上下文对象
 * @param avatar_type       头像的显示模式
 * SobotChatAvatarDisplayMode.Default:显示客服头像(默认)
 * SobotChatAvatarDisplayMode.ShowFixedAvatar:显示固定头像
 * SobotChatAvatarDisplayMode.ShowCompanyAvatar:显示console设置的企业名称
 * @param custom_avatar_url 如果需要显示固定头像，需要传入此参数，其他模式可以不传
 * @param isShowAvatar      是否显示头像
 */
SobotApi.setChatAvatarDisplayMode(getApplicationContext(),SobotChatAvatarDisplayMode.Default, "https://sobot-test.oss-cn-beijing.aliyuncs.com/console/66a522ea3ef944a98af45bac09220861/userImage/20191024164346682.PNG", false);

```


###40、关键字转人工
* 相似问题：    
	关键字转人工？
	智能转人工？       





###41、智能路由
* 相似问题：  
	排队优先级设置？    
	优先接待？    




