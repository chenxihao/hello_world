[toc]


# 设备端开发手册
# 1. 前言
本手册的编写，是基于开发者对mbed-os的开发有基本的了解。否则，请开发者先去[mbed-os开发者网站](https://developer.mbed.org/)学习一下。
# 2. 开发环境

|平台 | 名称|
|---|---|
|操作系统 | Windows 7/10等|
|硬件平台 | RDA 5991H |
|代码管理工具|Git|
|编译工具|mbed CLI |
|串口调试工具|xshell，putty等|

## 2.1 mbed CLI简介
mbed CLI是ARM提供的mbed命令行编译工具，下面将对它做简单的介绍，详细介绍请参照[mbed-os官网](https://docs.mbed.com/docs/mbed-os-handbook/en/latest/dev_tools/cli/#installing-mbed-cli)。
### 依赖工具
* **Python** 要求使用Python2.7.11以上版本，不兼容Python3
* **Git 和 Mercurial** mbed CLI同时支持Git和Mercurial 的代码库，所以两者都需要
  * Git - 支持1.9.5版本及以上.
  * Mercurial - 支持2.2.2版本及以上.
* **编译器或IDE工具链** 编译mbed-os工程，以下工具链可任择其一，DuerOS Light SDK目前仅支持ARM Compiler 5工具链
  * 编译器: GCC ARM, ARM Compiler 5, IAR.
  * IDE: Keil uVision, DS-5, IAR Workbench.
 
mbed CLI的依赖工具请自行安装好，可参照ARM提供的[视频教程](https://www.youtube.com/watch?v=cM0dFoTuU14)
### 安装mbed CLI
可以安装最新稳定版本
```
pip install mbed-cli
```
也可以安装开发版本
```
git clone https://github.com/ARMmbed/mbed-cli
python setup.py install
```
### 卸载mbed CLI
```
pip uninstall mbed-cli
```
### 创建工程
创建名为my-mbed的工程
```
mbed new my-mbed
```
### 导入工程
导入mbed的一个范例工程
```
mbed import https://github.com/ARMmbed/mbed-os-example-blinky
```
### 配置编译环境
配置工具链路径，以下为ARM Compiler 5的路径配置
```
mbed config -G ARM_PATH "C:\Program Files\ARM"
```
### 编译工程
以前面导入的工程为例，编译方式如下
```
cd mbed-os-example-blinky
mbed compile -t ARM -m K64F
```
可将ARM替换为mbed CLI支持的其它工具链，将K64F替换为其它目标平台
## 2.2 SDK及工具下载
以下为开发者开发过程中会用到的SDK及库下载链接：

|名称|版本|下载链接|说明|
|---|---|---|---|
| DuerOS Light SDK    |v1.1.0|[下载](http://open.duer.baidu.com/iot/download/DuerOS-Light-SDK-v1.1.0.rar)|包含mbed-os, dueros-light, demo, demo三个子目录|
|bootloader.bin|v1.1.0|[下载](http://open.duer.baidu.com/iot/download/bootloader.bin)|开启OTA功能时，会用到|

下面是烧写时会用到的工具，前两个工具是RDA提供的：

|名称|下载链接|RDA下载地址|
|---|---|-|
|Flash测试工具 |[下载](http://open.duer.baidu.com/iot/download/RDA5981_Flash_Test_Tool_0224.rar)|[由此进入](http://bbs.rdamicro.com/forum.php?mod=viewthread&tid=109&extra=page%3D1)|
|Merge Tool|[下载](http://open.duer.baidu.com/iot/download/MergeTool_V01.00.01_20170407.rar)|[由此进入](http://bbs.rdamicro.com/forum.php?mod=viewthread&tid=201&extra=page%3D2)|
|image-pack.py  |[下载](http://open.duer.baidu.com/iot/download/image-pack.py)|无|

# 3. DuerOS Light SDK介绍
DuerOS Light SDK是基于mbed-os的轻量级设备解决方案。DuerOS Light SDK提供的API可以大大简化设备端开发者的工作，只需少量的代码即可完成设备端连接云平台，录音，播放媒体文件，OTA升级等功能。DuerOS Light SDK主要包括CA（Connection Agent），Http，Media，Recoder，OTA等模块。
## 3.1 CA模块
连接设备端与云端，并简化设备端与云端之间的交互。CA模块提供了自动注册机制、设备监控命令路由框架、数据上报接口等，降低了设备注册到云的难度，降低了设备端开发者实现监控命令和数据上报的工作量。
    CA模块提供给用户的主要头文件包括：baidu_ca.h，baidu_ca_scheduler.h等。 baidu_ca.h提供了CA模块的API，baidu_ca_scheduler.h是对baidu_ca.h的封装，一般功能的开发，开发者只需使用baidu_ca_scheduler.h即可。
## 3.2 Http模块
提供client端通过http连接server的功能，简化连接步骤。主要头文件包括：baidu_http_client_c.h，baidu_http_client.h等，其中baidu_http_client.h是对baidu_http_client_c.h的封装，开发者只需使用baidu_http_client.h即可。
## 3.3 Media模块
提供本地或网络媒体文件的播放功能，目前可支持mp3，m4a，aac和wav格式。主要头文件包括：baidu_media_play.h，baidu_media_data_manager.h，media_manager.h等，开发者只需使用media_manager.h即可。
## 3.4 Recoder模块
提供录音功能。主要头文件包括：baidu_recorder.h，baidu_recorder_manager.h等，其中baidu_recorder_manager.h是对baidu_recorder.h的封装，开发者只需使用baidu_recorder_manager.h即可。
## 3.5 OTA模块
提供固件版本升级功能。OTA模块会在固件需要升级的时候，按照云端策略设置自动完成升级工作。需要注意的是，OTA功能需配合使用DuerOS的bootloader，否则OTA功能将无法使用。现在bootloader是以bin的形式提供，后期会开放源码。

# 4. 设备端开发
为了能让开发者迅速的掌握使用DuerOS Light SDK开发项目，下面将详细介绍一下设备端开发中的各个重要步骤。
## 4.1 创建工程
1. 创建工程目录，并将DuerOS Light SDK包解压到该目录，目录结构如下：

![图片](http://bos.nj.bpc.baidu.com/v1/agroup/d885abf72b2a1b1285baf471fb05e7c7d3a0e0c5)

2. 在工程目录下创建存放开发者自己代码的目录。并在该目录下，创建一个mbed_app.json文件用于配置设备相关的信息，mbed_app.json示例如下：
```
"target_overrides": {
    "UNO_91H": {
        "target.features_add": ["LWIP", "SDCARD", "GPADCKEY", "CONSOLE"],
        "target.macros_add": ["BD_FEATURE_NET_STACK_USING_WIFI", "BD_FEATURE_HTTPCLIENT_USING_WIFI"]
     },
    "K64F": {
        "target.features_add": ["LWIP"]
    }
}
```

在文件mbed-os\hal\targets.json中有各种target的定义，包括UNO_91H，K64F。示例代码中target_overrides部分，会override targets.json文件中UNO_91H，K64F的定义。以UNO_91H为例，target.features_add表示在原有的features定义中增加"LWIP", "SDCARD", "GPADCKEY", "CONSOLE"四个feature。target.macros_add表示增加2个宏定义"BD_FEATURE_NET_STACK_USING_WIFI", "BD_FEATURE_HTTPCLIENT_USING_WIFI"。具体规则请参照[mbed-os开发网站的详细说明](https://docs.mbed.com/docs/mbed-os-handbook/en/latest/advanced/config_system/)。
## 4.2 编写代码
以下是编写代码时必须注意的点，其它详细功能的使用请参考[5.主要接口类及API]
### 4.2.1 初始化工作
* 初始化SD卡，平台不同初始化的方式也不同，开发者需根据自己的平台来做初始化
```
//定义静态变量初始化SD卡
#if defined(TARGET_UNO_91H)
SDMMCFileSystem sd(GPIO_PIN9, GPIO_PIN0, GPIO_PIN3, GPIO_PIN7, GPIO_PIN12, GPIO_PIN13, "sd");
#elif defined(TARGET_K64F)
static SDFileSystem sd = SDFileSystem(D11, D12, D13, D10, "sd");
#endif
```
* 初始化flash（仅限TARGET_UNO_91H平台），开发者需根据实际使用硬件情况来做初始化
```
const unsigned int RDA_FLASH_SIZE = 0x400000;       //Flash Size
const unsigned int RDA_SYS_DATA_ADDR = 0x18204000;  //System Data Area, fixed size 4KB
const unsigned int RDA_USER_DATA_ADDR = 0x18205000; //User Data Area start address
const unsigned int RDA_USER_DATA_LENG = 0x3000;     //User Data Area Length

rda5981_set_flash_size(RDA_FLASH_SIZE);
rda5981_set_user_data_addr(RDA_SYS_DATA_ADDR, RDA_USER_DATA_ADDR, RDA_USER_DATA_LENG);
```
* 初始化Media模块
```
duer::MediaManager::instance().initialize();
```
* 连接网络：开发者可根据自身情况，调用mbed-os接口选择连接wifi或ethernet
* 初始化CA库
``` 
duer::Scheduler::instance().start();
```
### 4.2.2 SD卡的使用
SD卡支持FAT12 / FAT16 / FAT32格式，最大支持32Gb。初始化SD卡之后，可以类似"/sd/xxx"的路径访问sd卡上的文件。
### 4.2.3 其它注意事项
开发者需在代码中定义如下函数，以供DuerOS Light使用，否则工程会链接失败。
`void *baidu_get_netstack_instance(void)`，定义该函数提供WiFiStackInterface或EthernetInterface对象指针.
## 4.3 工程编译
工程的编译使用mbed-cli工具，开发者可参照以下命令编译自己的工程：
```
//在工程目录下执行以下命令
mbed compile --source PROJECT_NAME --source dueros-light --source mbed-os -m TARGET -t TOOL_CHAIN
//编译带OTA功能的请使用如下命令
mbed compile --source PROJECT_NAME --source dueros-light -DBD_FEATURE_ENABLE_OTA --source mbed-os -m TARGET -t TOOL_CHAIN
```
* PROJECT_NAME为工程名称
* TARGET为目标平台，目前DuerOS Light SDK支持*UNO_91H，K64F*
* TOOL_CHAIN为编译工具链，DuerOS Light SDK推荐使用*ARM*
* BD_FEATURE_ENABLE_OTA 开启OTA功能，默认是关闭的

## 4.4 烧录
### 4.4.1 烧录不带OTA功能操作
直接将编出的工程文件烧录到板子中即可。具体方法有(建议采用第一种)：

 - 通过板子自带命令loady
     1. 连接串口工具；
     2. 在串口终端出现“count_left=0”之前输入回车，会出现“Boot abort”；
     3. 输入loady指令，等待出现”Ready for binary”后，右键点击选择传输，选择YMODEM，用YMODEM发送；
     4. 选择并传输对应bin文件；
     
![图片](http://bos.nj.bpc.baidu.com/v1/agroup/d0a99806d66df5feb37c8319ea4e516995027a41)

![图片](http://bos.nj.bpc.baidu.com/v1/agroup/4cb71157f645ccd8a4a7719c329e6e774ae75d35)

 -  通过Flashtest工具
 RDA Flashtest工具打开后如下，参照图示进行设置：
 
 ![图片](http://bos.nj.bpc.baidu.com/v1/agroup/1c0d150f55a7afd51553f53239d585ed694334d4)
 
点击Setup按钮会弹出图下方的Setup窗口，参考图中所示进行设置。
之后点击Start按钮，后面待出现下面输出后，点击reset按钮即开始烧录。
```
Open port...
==============================
Running...
Waiting for plug in...
```
### 4.4.2 烧录带OTA功能操作

1. 使用脚本文件image-pack.py，为编译出的工程文件([4.3.2](#432-%E5%B7%A5%E7%A8%8B%E7%BC%96%E8%AF%91))添加版本信息：
`python image-pack.py xxx.bin y.y.y.y`xxx.bin为工程文件，y.y.y.y为版本号。
上面命令执行完成会生成对应的xxx.bl文件，将xxx.bl修改为xxx.bin文件。

2. 下载bootloader.bin，使用Merge File工具将bootloader.bin与步骤1生成的xxx.bin合并为一个bin进行烧录:
![图片](http://bos.nj.bpc.baidu.com/v1/agroup/a78c662f5263f25929105e685e443359307279a1)
3. 参考([4.4.1](#441-%E7%83%A7%E5%BD%95%E4%B8%8D%E5%B8%A6ota%E5%8A%9F%E8%83%BD%E6%93%8D%E4%BD%9C))将merge后的bin文件进行烧录。
# 5. 主要接口类
本节将详细介绍DuerOS Light SDK提供给开发者的主要接口类与API，类中未提供说明的public API开发者可忽略，之后会做优化。
## Scheduler类
**所属头文件**

baidu_ca_scheduler.h

**功能描述**
单例类，封装CA模块，提供与云端的交互功能
### static Scheduler &instance()
**功能描述**

获取Scheduler实例，第一次调用时，会初始化CA模块

**参数**

无

**返回值**

Scheduler对象
### int set_on_event_listener(IOnEvent* listener)
**功能描述**

设置事件监听者

**参数**

|类型|名称|描述|
|-|-|-|
|IOnEvent* |listener|Scheduler事件的监听者|

**返回值**

0：成功，-1：失败

**其它说明**

IOnEvent定义如下：
```
/**
 * @brief Scheduler 的事件回调接口
 */
class IOnEvent {
public:
    /**
     * @brief Scheduler 在启动时的回调函数接口
     */
    virtual int on_start() = 0;
    /**
     * @brief Scheduler 在停止时的回调函数接口
     */
    virtual int on_stop() = 0;
    /**
     * @brief Scheduler 在有数据通信时的回调函数接口
     * @param[in] action 云端下发的url
     */
    virtual int on_action(const char* action) = 0;
    /**
      * @brief Scheduler 在有数据通信时的回调函数接口
      * @param[in] data jason格式，包含云端下发的所有数据
      */
     virtual int on_data(const char* data) = 0;
};
```
### int add_controll_points(const bca_res_t list_res[], bca_size_t list_res_size)
**功能描述**

设置云端控制点，提供云端对设备端的回调点

**参数**

|类型|名称|描述|
|-|-|-|
|const bca_res_t[] |list_res|控制点数组|
|bca_size_t |list_res_size|控制点数组大小|

**返回值**

0：成功，-1：失败

**其它说明**
bca_res_t 是一个结构体，定义如下
```
/*
 * The resource operation permission
 */
typedef enum
{
    BCA_RES_OP_GET      = 0x01,     //< Get operation allowed
    BCA_RES_OP_PUT      = 0x02,     //< Put operation allowed
    BCA_RES_OP_POST     = 0x04,     //< Post operation allowed
    BCA_RES_OP_DELETE   = 0x08      //< Delete operation allowed
} bca_resource_operation_e;

/*
 * The resource mode
 */
typedef enum
{
    BCA_RES_MODE_STATIC,            //< Static resources have some value that doesn't change
    BCA_RES_MODE_DYNAMIC,           //< Dynamic resources are handled in application
} bca_resource_mode_e;

typedef bca_status_t (*bca_notify_f)(bca_context, bca_msg_t* , bca_addr_t* );

/*
 * The resource for user
 */
typedef struct _bca_resource_s
{
    bca_u8_t    mode:2;     //< the resource mode, SEE in ${link bca_resource_mode_e}
    bca_u8_t    allowed:6;  //< operation permission, SEE in ${link bca_resource_operation_e}
    char*       path;       //< the resource path identify

    union
    {
        bca_notify_f   f_res;   //< dynamic resource handle function, NULL if static
        struct {
            void*       data;   //< static resource value data, NULL if dynamic
            bca_size_t  size;   //< static resource size
        } s_res;
    } res;
} bca_res_t;
```
**示例代码**
```
static bca_status_t media_stop(bca_context ctx, bca_msg_t* msg, bca_addr_t* addr)
{
    ...
    return BCA_NO_ERR;
}
...
bca_res_t res[] = {
    {BCA_RES_MODE_DYNAMIC, BCA_RES_OP_PUT, "stop", media_stop},
    {BCA_RES_MODE_DYNAMIC, BCA_RES_OP_PUT | BCA_RES_OP_GET, "volume", set_volume},
    {BCA_RES_MODE_DYNAMIC, BCA_RES_OP_PUT, "shutdown", shutdown},
    {BCA_RES_MODE_DYNAMIC, BCA_RES_OP_PUT | BCA_RES_OP_GET, "mode", set_mode},
    {BCA_RES_MODE_DYNAMIC, BCA_RES_OP_GET, "power", get_power},
};
Scheduler::instance().add_controll_points(res, sizeof(res) / sizeof(res[0]));
```
### int start()
**功能描述**

开始与云端建立连接

**参数**

无

**返回值**

0：成功，-1：失败
### int stop()
**功能描述**

结束与云端的连接

**参数**

无

**返回值**

0：成功，-1：失败

### int report(const Object &data)
**功能描述**

向云端上传数据

**参数**

|类型|名称|描述|
|-|-|-|
|const Object& |data|各种数据可以按key-value的形式放入Object对象|

**返回值**

0：成功，-1：失败

**示例代码**
```
duer::Object data;
data.putInt("time", us_ticker_read());
duer::Scheduler::instance().report(data);
```
### int send_content(const void* data, size_t size, bool eof = false)
**功能描述**

向云端上传语音数据，数据可分多次传输，最后一次传输需将eof参数设为true

**参数**

|类型|名称|描述|
|-|-|-|
|const void* |data|语音数据|
|size_t |size|data数据的大小|
|bool|eof|默认值为false，为true时表示数据为结束部分|

**返回值**

0：成功，-1：失败
### int response(const bca_msg_t* req, int msg_code, const char* payload = NULL)
**功能描述**

对云端的调用做出回复，在调用点函数中使用

**参数**

|类型|名称|描述|
|-|-|-|
|const bca_msg_t* |req|回复针对的消息对象，即调用点中收到的消息对象|
|int|msg_code|回复消息的类型|
|const char*|payload |回复消息的内容，默认值为NULL|

**返回值**

0：成功，-1：失败

**示例代码**
```
static char mode[10] = {0};
int msg_code = BCA_MSG_RSP_CHANGED;
if (msg) {
    if (msg->msg_code == BCA_MSG_REQ_GET) {
        LOG("mode get: %s", mode);
    } else if (msg->msg_code == BCA_MSG_REQ_PUT) {
        if (msg->payload && msg->payload_len > 0) {
            strncpy(mode, (char* )msg->payload, msg->payload_len);
            LOG("mode set: %s", mode);
        } else {
            msg_code = BCA_MSG_RSP_FORBIDDEN;
            mode[0] = 0;
            LOG("mode set invalid");
        }
    }
    Scheduler::instance().response(msg, msg_code, mode);
}
```
### int clear_content()
**功能描述**

结束上传并清理还未上传云端的数据

**参数**

无

**返回值**

0：成功，-1：失败

## HttpClient类
**所属头文件**

baidu_http_client.h

**功能描述**

提供Client端通过http连接server的功能
### void register_data_handler(data_out_handler_cb data_hdlr_cb, void* p_usr_ctx)
**功能描述**

注册接收server端数据的回调函数

**参数**

|类型|名称|描述|
|-|-|-|
|data_out_handler_cb |data_hdlr_cb|回调函数指针，由该函数处理数据|
|void* |p_usr_ctx|传入回调函数的用户参数指针，可以为空|

**返回值**

无

**其它说明**

data_out_handler_cb的定义如下：
```
//to tell data output callback user that if the current data block is first block or last block
typedef enum data_pos {
    DATA_FIRST  = 0x1,
    DATA_MID    = 0x2,
    DATA_LAST   = 0x4
} e_data_pos;
typedef int (*data_out_handler_cb)(void* p_user_ctx, e_data_pos pos, const char* buf, size_t len, const char* type);
```
回调函数参数说明

|类型|名称|描述|
|-|-|-|
|void* |p_user_ctx|用户注册回调函数时，传入的参数指针|
|e_data_pos |pos|表明此次接收数据段在整个数据中的位置|
|const char*|buf|接收的数据|
|size_t|len|接收的数据长度|
|const char*|type|接收的数据类型|

**示例代码**
```
#define BAIDU_HTTP_CLIENT_TEST_URL  "http://mqtt.org/"
static int http_client_test_output_handler(void* p_user_ctx, e_data_pos pos, const char* buf, size_t len, const char* type)
{   
    if (buf && len){
        printf("%s", buf);
    }
    return 0;
}


int main() 
{
    HttpClient hc_inst;
    hc_inst.register_data_handler(http_client_test_output_handler, NULL);
    int ret = hc_inst.get(BAIDU_HTTP_CLIENT_TEST_URL);
    printf("http client return code:%d\n", ret);

    Thread::wait(osWaitForever);
    return 0;
}
```
### e_http_result get(const char* url)
**功能描述**

根据url连接server，获取媒体文件数据

**参数**

|类型|名称|描述|
|-|-|-|
|const char* |url|要连接的url地址|

**返回值**

连接成功返回HTTP_OK，e_http_result 定义如下：
```
//http client results
typedef enum http_result {
    HTTP_OK = 0,             ///<Success
    HTTP_PROCESSING,         ///<Processing
    HTTP_PARSE,              ///<url Parse error
    HTTP_DNS,                ///<Could not resolve name
    HTTP_PRTCL,              ///<Protocol error
    HTTP_NOTFOUND,           ///<HTTP 404 Error
    HTTP_REFUSED,            ///<HTTP 403 Error
    HTTP_ERROR,              ///<HTTP xxx error
    HTTP_TIMEOUT,            ///<Connection timeout
    HTTP_CONN,               ///<Connection error
    HTTP_CLOSED,             ///<Connection was closed by remote host
    HTTP_NOT_SUPPORT,        ///<not supported feature
    HTTP_REDIRECTTION,       ///take a redirection when http header contains 'Location' 
    HTTP_FAILED=-1,
} e_http_result;
```
## MediaManager类
**所属头文件**

baidu_media_manager.h

**功能描述**

管理多媒体文件播放，同一时间只能播放单个文件

**类型介绍**

MediaPlayerStatus为media player的状态类型，定义如下
```
enum MediaPlayerStatus {
    MEDIA_PLAYER_IDLE,
    MEDIA_PLAYER_PLAYING,
    MEDIA_PLAYER_PAUSE
};
```
### static MediaManager& instance()
**功能描述**

获取MediaManager单例

**参数**

无

**返回值**

MediaManager单例对象

### bool initialize()
**功能描述**

初始化MediaManager，第一次调用有效，使用媒体播放功能前必须调用该接口做初始化。

**参数**

无

**返回值**

初始化是否成功
### MediaPlayerStatus play_url(const char* url)
**功能描述**

播放网络媒体文件

**参数**

|类型|名称|描述|
|-|-|-|
|const char* |url|网络媒体文件的url地址|

**返回值**

media player的上一个状态
### MediaPlayerStatus play_local(const char* path)
**功能描述**

播放本地媒体文件

**参数**

|类型|名称|描述|
|-|-|-|
|const char* |path|本地媒体文件路径|

**返回值**

media player的上一个状态
### MediaPlayerStatus pause_or_resume()
**功能描述**

播放状态下暂停播放，暂停状态下恢复播放，其它状态调用无效

**参数**

无

**返回值**

media player的上一个状态
### MediaPlayerStatus stop()
**功能描述**

停止播放当前文件

**参数**

无

**返回值**

media player的上一个状态
### MediaPlayerStatus get_media_player_status()
**功能描述**

获取media player的当前状态

**参数**

无

**返回值**

media player的当前状态
### int register_stop_callback(media_player_stop_callback callback)
**功能描述**

注册回调函数，该函数在音乐播放结束时会被调用，回调函数最多可注册5个

**参数**

callback为回调函数指针
```
typedef void (*media_player_stop_callback)();
``` 
**返回值**

注册成功返回0，失败返回-1

### int unregister_stop_callback(media_player_stop_callback callback)
**功能描述**

注销回调函数

**参数**

callback为回调函数指针
```
typedef void (*media_player_stop_callback)();
``` 
**返回值**

注册成功返回0，失败返回-1
### void set_volume(unsigned char vol);
**功能描述**

设置音量大小
**参数**

|类型|名称|描述|
|-|-|-|
|unsigned char |vol|音量值，有效范围为0~15|
 
**返回值**

无

## RecorderManager类
**所属头文件**

baidu_recorder_manager.h

**功能描述**

管理录音功能

### int start()
**功能描述**

开始录音

**参数**

无

**返回值**

0：成功，-1：失败

### int stop()
**功能描述**

结束录音

**参数**

无

**返回值**

0：成功，-1：失败

### int set_listener(Recorder::IListener* listener)
**功能描述**

设置监听者，监听者可在回调函数中获得录音状态及数据

**参数**

|类型|名称|描述|
|-|-|-|
|Recorder::IListener*  |listener|录音状态和数据的监听者|

**返回值**

0：成功，-1：失败

**其它说明**

Recorder::IListener定义如下
```
class IListener {
public:
    virtual int on_start() = 0;  // 开始录音时调用
    virtual int on_resume() = 0; // 暂时未启用，空实现即可
    virtual int on_data(const void* data, size_t size) = 0; // 接收录音数据时调用
    virtual int on_pause() = 0;  // 暂时未启用，空实现即可
    virtual int on_stop() = 0;   // 结束录音时调用
    virtual ~IListener() = 0;
};
```
