
## 操作场景

假设一款智能灯接入到腾讯云物联网开发平台，通过物联网开发平台可以远程控制灯的亮度、颜色、开关，并实时获取智能灯上报到开发平台的数据。本文档主要指导您如何基于TencentOS-tiny物联网操作系统，使用MQTTS协议在物联网开发平台控制台接入智能灯。

## 前提条件

为了通过下面的步骤快速理解该业务场景，需要做好以下准备工作：
- 申请物联网开发平台服务。
- 准备 TencentOS-tiny 官方 EVB_MX_Plus 开发板以及一个 ESP8266 Wi-Fi 模组。

## 控制台操作步骤

### 创建项目

1. 登录 [物联网开发平台控制台](https://cloud.tencent.com/login?s_url=https%3A%2F%2Fconsole.cloud.tencent.com%2Fiotexplorer)。
2. 点击新建项目，填写项目名称和项目描述。
![](https://main.qcloudimg.com/raw/948f0f074167fbf4765de571d4fdb84e.jpg)
 - **项目名称**：支持中文、英文、数字、下划线的组合，最多不超过20个字符。例如“智能灯演示”。
 - **项目描述**：最多不超过80个字符。
3. 项目基本信息填写完成后，单击【保存】，即可完成新建项目。
4. 项目新建成功后，即可新建产品。

### 新建产品

1. 进入该项目的产品列表页面，单击【新建产品】.
2. 在新建产品页面，填写产品基本信息。
![](https://main.qcloudimg.com/raw/5632337cf6b43546f83d4cf9a92a18dd.png)
  - **产品名称**：支持中文、英文、数字、下划线的组合，最多不超过20个字符。例如“智能灯”。
  - **产品类型**：选择【智慧城市】>【公共事业】>【路灯照明】。
  - **设备类型**：选择【设备】。
  - **认证方式**：选择【密钥认证】。
  - **通信方式**：任意选择其一。
  - **数据协议**：选择【数据模板】。
  - **描述**：最多不超过80个字符。
3. 产品信息填写完成后，单击【保存】，即可完成新建产品。
4. 产品新建成功后，可在产品列表页查看到“智能灯”。
![](https://main.qcloudimg.com/raw/3217d954429b125d08e1c1c81690aa08.png)

### 创建数据模板

当新建产品将“产品类型”选择为【路灯照明】时，此时单击产品进入产品详情页，系统将会自动生成标准功能。
![](https://main.qcloudimg.com/raw/f78bde95fd38ce6b7e27c65a81742dea.png)

### 创建测试设备

在【设备调试】页面中，单击【新建设备】，设备名为 dev001。
![](https://main.qcloudimg.com/raw/4f7f39df544ce19cf38126ddd79a49b3.png)
## 开发板实物操作

### 硬件连接

开发板实物如下：

![](https://main.qcloudimg.com/raw/4f47e4d3ba910e4528c9738c0ed65010.png)

1. 使用USB线连接到开发板顶端的USB接口，在为开发板供电的同时，可以在PC端通过串口调试助手查看输出日志。
2. 使用ST-Link下载器连接开发板右侧的接口，为开发板下载程序。
3. 将开发板配套的ESP8266模组插入到开发板右侧的通信模组接口。

### 串口准备

1. 硬件连接成功后，打开 PC 上的设备管理器，即可查看开发板所对应的串口（请确保已安装 CH340 驱动）。

![](https://main.qcloudimg.com/raw/5c44a520e8d0bc22d0e9849d8bcf3868.png)

2. 打开串口工具，做好相应配置后，打开串口：

  - 端口号。本例中为 COM20。
  - 波特率。本例中为 115200。

![](https://main.qcloudimg.com/raw/320e72b7721bf000829bc1e65cc75236.png)

### 属性上报和控制命令下发操作步骤

#### Step1. 下载官方例程

1. 使用git工具下载TencentOS-tiny源码，也可以访问[Github](https://github.com/Tencent/TencentOS-tiny)官方仓库下载：

```bash
git clone https://github.com/Tencent/TencentOS-tiny.git
```

2. 下载之后，进入TencentOS-tiny\board\TencentOS_tiny_EVB_MX_Plus\KEIL\qcloud_iot_explorer_sdk_data_template目录：

![](https://main.qcloudimg.com/raw/5233e111b7caa55d9f1b87ad63de5089.png)

3. 示例工程中包含STM32L431外设驱动、TencentOS-tiny内核、AT框架、SAL框架、腾讯云C-SDK，以及示例程序。

#### Step2. 配置修改

1. 双击 TencentOS_tiny.uvprojx 打开工程（请确保已经安装好Keil-MDK开发环境）。
2. 修改HAL_Device_tencentos_tiny.c文件，在

```
TencentOS-tiny\components\connectivity\qcloud-iot-explorer-sdk\port\TencentOS_tiny
```

目录中：

![](https://main.qcloudimg.com/raw/cadab6199c68fc70debc7e02a6580731.png)

3. 将上图红色线框中的数据分别替换为控制台 **“设备详情页”** 中的参数并保存。

 * 产品 ID： 将控制台的产品 ID ，复制到上图 sg_product_id。
 * 设备名称： 将控制台的设备名称，复制到上图 sg_device_name。
 * 设备密钥：将控制台的设备密钥，复制到上图sg_device_secret。

4. 修改entry.c中的WIFI网络接入配置信息：

![](https://main.qcloudimg.com/raw/d1dc0309a19ff454112f64fbc3111da2.png)

#### Step3. 编译

单击 MDK 工具栏【Rebuild All】，编译整个工程。

#### Step4. 下载

单击 MDK 工具栏【Download】，下载编译好的固件。

#### Step5. 查看运行结果

在串口助手中可以看到设备在上报之后处于等待平台下发控制指令的状态：

![](https://main.qcloudimg.com/raw/1a5c4c6cc1e3c7e913a92a3e9949dafb.png)

### 物联网平台查看上报数据

1. 保持系统处于运行状态。
2. 进入【控制台】>【产品开发】>【设备调试】，可查看到设备 "dev001" 。
3. 单击【调试】，可进入设备详情页。


![](
https://main.qcloudimg.com/raw/b873b65c618480ede6485073090bc1f2.png)

4. 单击【设备属性】，可查询设备上报到开发平台的最新数据及历史数据。

  - 设备属性的最新值：会显示设备上报的最新数据。
  - 设备属性的更新时间：显示数据的更新时间。

![](https://main.qcloudimg.com/raw/c08da8cf2b2748d93368059740cbe6fa.png)

5. 单击【查看】，可查看某个属性的历史上报数据。

### 物联网平台在线调试

1. 在控制台【设备调试】列表，单击【调试】，进入在线调试。

2. 设置电灯开关为on，颜色为Green，亮度为3，点击【发送】。

![](https://main.qcloudimg.com/raw/7298a5f4b0c831f0814a4a59325e4d78.png)

3. 查看系统打印的串口日志，可以判断系统成功收到并响应了下发的控制指令：

![](https://main.qcloudimg.com/raw/2691916612ade54ff47636534e160cd7.png)

4. 同时，在开发板的OLED显示屏幕上，可以看到系统模拟出的智能灯状态：

![](https://main.qcloudimg.com/raw/7f2d51718313d34a050e1ba2a9992d18.png)

### 设备行为调用操作步骤

1. 在云端的数据模板中手动新建一个设备行为功能：

![](https://main.qcloudimg.com/raw/19ab99e774389887eac8338f5238e9bc.png)

2. 修改工程文件，在`data_template_sample.c`文件中使能Action：

![](https://main.qcloudimg.com/raw/8f605b70838ae7c12133617977d95c0b.png)

3. 重新编译下载，按复位使系统运行。

4. 在云端下发设备行为调用：

![](https://main.qcloudimg.com/raw/4320c5a421a5c8d54ac7ee9120e5912c.png)

5. 在串口助手中可以看到设备行为被调用：

![](https://main.qcloudimg.com/raw/8361ab21c5c2ff6dd1c33f82892ba918.png)

### 设备事件上报操作步骤

1. 修改工程文件中的`config.h`文件，将设备行为调用关闭，开启设备事件支持：

![](https://main.qcloudimg.com/raw/602e0f0d6d6049f74b30c9b46dd7aa46.png)

2. 修改工程文件中的`data_template_sample.c`文件，关闭设备行为调用示例，开启事件上报示例：

![](https://main.qcloudimg.com/raw/9dfd57b19dd6ce96b4e24704905b48a4.png)

3. 编译程序，下载到开发板中，复位，使系统运行。

4. 在串口助手中可以看到设备上报事件的日志：

![](https://main.qcloudimg.com/raw/5825feb3252c51391cee08670117bf65.png)

5. 在平台端可以看到设备上报事件的日志：

![](https://main.qcloudimg.com/raw/86b8032eccfd6a1901f70ad828ec3cdb.png)

  

