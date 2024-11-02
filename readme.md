# API list

* 所有函数返回值都是Json Object，成功返回`{"code":"success"}`，失败返回`{"code":"error"}`
* 所有函数参数值都是Json Object，格式为`{键:值,键:值,...}`
* text键值是String或String数组，如果业务数据是其它类型用`String()`转换下，传参时注意
* 热敏打印机是单线程模式，多线程调用打印函数会导致异常
* 图片打印有两种选择，一种直接打印，适合简单的小图。一种是保存到打印机Flash中，1次保存重复打印，适合复杂大图或者对图片打印效果有高要求的情况
* 对于部分串口打印机直接打印图片有断线的问题，可以选择保存到打印机Flash中打印，或者联系我们获取打印机固件更新工具来更新到慢速打印模式来更好的兼容直接打印图片

调用插件方法
```
//加载插件
const pos = uni.requireNativePlugin("FeiHai-SmartPosModule")
//初始化打印机，一般只需要调用一次
pos.printerInit()
//连接打印机
pos.printerConnect()
//执行打印任务
...
...
//断开打印机
pos.printerConnect()
```


###### 初始化打印机
`printerInit()`
参数：无
说明：默认检测是否有USB打印机，如果有则初始化USB打印机，否则默认内置串口打印机。USB打印机默认为80mm，内置串口打印机58mm
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}


###### 连接打印机
`printerConnect()`
参数：无
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}

###### 断开打印机
`printerDisconnect()`
参数：无
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}

###### 缺纸检查
`printerCheckPaper()`
参数：无
返回值：Json对象。有纸返回{"code":"success"}，缺纸返回{"code":"error"}

###### 设置加热参数
`printerSetHeatingParam()`
参数：dots 最大加热点数，范围 0-255，单位(8dots)。默认值 7
      duration 加热时长，范围 0-255，单位(10us)。默认值 80
      interval 加热间隔时长，范围 0-255，单位(10us)，默认值 2
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}
说明：仅部分串口打印机支持，一般不建议修改，设置不当可能造成无法打印或打印质量变差
举例：`printerSetHeatingParam({dots:15,duration:100,interval:10})`

###### 设置行间距
`printerSetLineSpacing()`
参数：line_spacing 行间距，范围0-255，单位点
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}
说明：默认行间距32点，行间距指两行字符顶端之间的距离
举例：`printerSetLineSpacing({line_spacing:15})`

###### 设置打印对齐方式
`printerSetTextAlignMode()`
参数：align 0-左对齐，1-居中，2-右对齐。默认左对齐
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：对齐方式对字符、条码、QR码都有效
举例：`printerSetTextAlignMode({align:0})`

###### 设置全局字符编码
`printerSetTextEncodeing()`
参数：encoding 字符编码，具体编码常量定义参考demo
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：该函数会改变打印机的字符编码页，务必为将要打印的字符设置正确的编码
举例：`printerSetTextEncodeing({encoding:CODE_PAGE_GB18030})`

###### 设置字符打印模式
`printerSetTextMode()`
参数：mode 打印模式，具体模式常量定义参考demo
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：可以同时设置多种模式
举例：`printerSetTextMode({mode:FONT_DOUBLE_HEIGHT|FONT_DOUBLE_WIDTH|FONT_BOLD})`

###### 设置下划线高度
`printerSetTextUnderline()`
参数：underline 下划线高度，范围0，1，2，单位点
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：为0时代表无下划线
举例：`printerSetTextUnderline({underline:2})`

###### 设置字符放大倍数
`printerSetTextScale()`
参数：width_scale 宽度放大倍数
      height_scale 高度放大倍数。范围0-7对应1-8倍
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：设置字符放大后行间距也跟着放大
举例：`printerSetTextScale({width_scale:2, height_scale:4})`

###### 打印缓冲区数据并走纸n行
`printerLineFeed()`
参数：line_feed 走纸行数，范围0-255，为0时代表打印缓冲区数据并换行
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：默认行间距32点
举例：`printerLineFeed({line_feed:2})`

###### 打印一行虚线并换行
`printerDottedLine()`
参数：无
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}

###### 打印条形码
`printerPrintBarcode()`
参数：text 条码字符串
      hri_mode HRI字符打印位置，具体常量定义参考demo
	  hri_size HRI字符字体选择，0:12x24 1:9x17
	  width 条形码基线宽度，范围1-6，默认2
	  height 条形码高度，范围0-255 单位点数
	  encoding 条形码编码格式，具体编码常量定义参考demo
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：如果条码数据比较长，width需要设置为1。太长会无法打印
举例：`printerPrintBarcode({text:"ABC1234567890",hri_mode:2,hri_size:0,width:2,height:80,encoding:"code93"})`

###### 打印QR码
`printerPrintQR()`
参数：text QR码内容
      size QR基块大小（nxn正方形大小），范围0-16
	  ecc QR码纠错等级，具体常量定义参考demo
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：设置字符放大后行间距也跟着放大
举例：`printerPrintQR({data:"https://www.pay-device.com",size:8,ecc:2})`

###### 水平制表打印一行数据并换行
`printerPrintTable()`
参数：text 列字符串数组，列数最大32(384/12=32)
      offset 列偏移量数组，单位12点
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：
举例：`printerPrintTable({text:["Pizza","x3","60.0"],offset:[2,9,19]})`

###### 打印一行字符并换行
`printerPrintLine()`
参数：text 要打印的字符串
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：字符长度超过最大宽度时自动换行
举例：`printerPrintLine({text:"123456"})`

###### 打印两列字符并换行，两端对齐
`printerPrintLineAlignLeftRight()`
参数：left 左列字符串数据
      right 右列字符串数据
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：
举例：`printerPrintLineAlignLeftRight({left:"Pizza",right:"x2"})`

###### 打印多列字符并换行
`printerPrintLineMultiCols()`
参数：text 字符串数组
      width 字符串宽度比例数组
      align 字符串对齐方式数组
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：58mm打印最大宽度48mm=384点，如果是默认12x24点字体，一行最多384/12=32个英文字符或384/24=16个中文字符。宽度比例不合适时，超出部分会丢弃
举例：
```
printerPrintLineMultiCols({
	text:["Pizza","x3","30.0"],
	width:[6,3,3],
	align:[ALIGN_LEFT,ALIGN_RIGHT,ALIGN_RIGHT]
})`
```

###### 打印图片
`printerPrintBitmap()`
参数：path 图片路径
      threshold 黑白二值化的阈值，根据图片色彩需要调整，范围0-255。
      inverse 是否反白打印 0-正常模式，1-反白模式
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：一般把图片放到/sdcard/目录下方便打印机调用
举例：`printerPrintBitmap({path:"/sdcard/test.bmp", threshold:200, inverse:0})`

###### 保存图片到打印机Flash(掉电保存，部分打印机支持)
`printerSaveBitmap()`
参数：path 图片路径
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：图片要求宽高是8的整数倍，尺寸不要超过384x384。保存一次后可以重复读取打印，另外不要频繁保存或删除flash中的图片，可能会导致flash异常
举例：`printerSaveBitmap({path:"/sdcard/test.bmp"})`

###### 删除打印机Flash中的图片(部分打印机支持)
`printerDeleteBitmap()`
参数：无
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：不要频繁保存或删除flash中的图片，可能会导致flash异常
举例：`printerDeleteBitmap()`

###### 打印Flash中的图片(部分打印机支持)
`printerPrintFlashBitmap()`
参数：zoom 0-原始尺寸，1-水平放大1倍，2-2垂直放大1倍，3-水平和垂直都放大1倍
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：超出打印宽度的部分会丢弃
举例：`printerPrintFlashBitmap({zoom:0})`


###### 发送字符到打印机缓冲区
`printerSendText()`
参数：text 字符串数据
      encoding 字符串编码
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：这里只是发送到打印机缓冲区，在调用printerLineFeed后才会打印，通常用于在一行中打印不同编码字符的情况
      该函数只是临时改变打印机内字符编码页，调用完后会恢复成函数printerSetTextEncodeing()设置的全局编码 
举例：打印"你好世界 Привет мир"
```
printerSendText({text:'你好世界',encoding:CODE_PAGE_GB18030})
printerSendText({text:' Привет мир',encoding:CODE_PAGE_CP1251})
printerLineFeed({line_feed:1})
```

###### 获取打印机接口类型
`printerGetType()`
参数：无
返回值：Json对象。成功返回{code:"success", msg:"xxxx"}，失败返回{code:"error"}
        打印机接口类型在msg键值中，USB接口:"usb",串口接口:"serial"
说明：
举例：
```
printerGetType()
返回{code:"success",msg:"usb"}
返回{code:"success",msg:"serial"}
```

###### 切纸
`printerCutPaper()`
参数：mode 切纸模式
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：通常80mm USB接口打印机才支持切刀，部分打印机只支持半切
举例：
```
全切
printerCutPaper({mode:0})
半切
printerCutPaper({mode:1})
```
###### 打开钱箱
`cashBoxOpen()`
参数：无
返回值：Json对象。成功返回{"code":"success"}，失败返回{"code":"error"}


###### 打开串口设备
`uartOpen()`
参数：port 串口设备节点，例如：/dev/ttyS1，/dev/ttyUSB0
      baudrate 串口波特率，例如：9600，115200
      databits 数据位位数，范围5-8
      stopbits 停止位位数，范围1，2
	  parity   校验位参数，none-无 even-偶数校验 odd-奇数校验
	  flowctrl 是否开启流控，none-无 sw-软件流控XON/XOFF hw-硬件流控RTS/CTS
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
说明：具体参数按照所接串口设备文档设置
举例：打开ttyS1串口，波特率115200，数据位8，停止位1，校验位无，无流
```
uartOpen({port:node, baudrate:115200, databits:8, stopbits:1, parity:"none", flowctrl:"none"})
```

###### 关闭串口设备
`uartClose()`
参数：port 串口设备节点，例如：/dev/ttyS1，/dev/ttyUSB0
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
举例：关闭串口设备/dev/ttyS1
```
uartClose({port:"/dev/ttyS1"})
```

###### 发送数据到串口设备
`uartWrite()`
参数：port 串口设备节点，例如：/dev/ttyS1，/dev/ttyUSB0
      data 要发送的数据，格式为十六进制字节数组，2位代表一个字节
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
举例：发送命令AA,BB,CC到/dev/ttyS1串口设备
```
uartWrite({port:"/dev/ttyS1", data:"AABBCC"})
```

###### 从串口设备接收数据
`uartRead()`
参数：port 串口设备节点，例如：/dev/ttyS1，/dev/ttyUSB0
返回值：Json对象。成功返回{code:"success", msg:"xxxx"}，失败返回{code:"error"}
        返回的数据在msg键值中，如果返回数据为空则msg键值为null
举例：从/dev/ttyS1串口设备接收数据
```
ret = pos.uartRead({port:node})
if (ret.code == "error") {
	console.log("read error");
} else {
	if (ret.msg != null) {
		console.log("respone:"+ret.msg);
	} else {
		console.log("no respone");
	}
}

```

###### 调用原生API发送广播
`sendBroadcast()`
参数：action 广播名
      extra 需要传递的数据 格式：逗号分割键名键值，英文分号分割多个数据，举例："msg1,hello;msg2,world;"
返回值：Json对象。成功返回{code:"success"}，失败返回{code:"error"}
举例：进入和退出Kiosk全屏模式
```
sendBroadcast({action:"com.paydevice.action.CONTROL_STATUSBAR",extra:"cmd,hide;"})
sendBroadcast({action:"com.paydevice.action.CONTROL_STATUSBAR",extra:"cmd,show;"})
```

