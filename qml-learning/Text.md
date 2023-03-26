> Text 可以显示纯文本和富文本，并设置与文本相关的属性。

例如:

```qml
import QtQuick 2.14
import QtQuick.Window 2.14

Window {
    visible: true
    width: 640
    height: 480
    title: qsTr("Hello World")


    Column{

        Text{
            id : text1
            //简单文本
            font.family: "Microsoft Yahei"
            font.pixelSize: 24
            text: "<font color=\"#056643\">hello <br> world</font>"
            textFormat: Text.PlainText
        }

        Text{
            id : text2
            //富文本
            font.family: "Microsoft Yahei"
            font.pixelSize: 24
            text: "<font color=\"#056643\">hello <br> world</font>"
        }
    }


}
```

# 属性

Text有下面属性

* advance : 只读属性，显示从文本项第一个字符的基线原点到文本项中第一个字符的基线原点的距离
* antialiasing : 用于确定文本是否应使用抗锯齿。只有呈现类型为 Text.NativeRendering 的文本才能禁用抗锯齿
* baseUrl : 此属性指定用于解析文本中的相对 URL 的基本 URL
* bottomPadding : 底部内边距
* clip : 此属性保存文本是否被剪裁，如果要在有限的空间中显示可能较长的文本，则可能需要改用。elide
* color : 颜色
* contentHeight : 只读属性，内容的高度
* contentWidth : 只读属性，内容的宽度
* effectiveHorizontalAlignment : 只读属性，有效水平对齐
* elide : 字符显示时省略的方式
* font.bold : 字体样式粗体
* font.capitalization : 字体样式大小写
* font.family : 字体族
* font.hintingPreference : 设置文本上的首选提示
* font.italic : 斜体
* font.kerning : 字据调整
* font.letterSpacing : 字母间距
* font.pixelSize : 像素尺寸
* font.pointSize : 磅值
* font.preferShaping : bool
* font.strikeout : bool
* font.styleName : string
* font.underline : bool
* font.weight : enumeration
* font.wordSpacing : real
* fontInfo.bold : bool
* fontInfo.family : string
* fontInfo.italic : bool
* fontInfo.pixelSize : string
* fontInfo.pointSize : real
* fontInfo.styleName : string
* fontInfo.weight : int
* fontSizeMode : enumeration
* horizontalAlignment : enumeration
* hoveredLink : string
* leftPadding : real
* lineCount : int
* lineHeight : real
* lineHeightMode : enumeration
* linkColor : color
* maximumLineCount : int
* minimumPixelSize : int
* minimumPointSize : int
* padding : real
* renderType : enumeration
* rightPadding : real
* style : enumeration
* styleColor : color
* text : string
* textFormat : enumeration
* topPadding : real
* truncated : bool
* verticalAlignment : enumeration
* wrapMode : enumeration 