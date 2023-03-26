*Canvas是一个允许绘制直线和曲线、简单和复杂的形状、图形和引用的图形图像。它还可以添加文本、颜色、阴影、渐变和图案，并执行低级别像素操作。Canvas输出可以另存为图像文件或序列化到URL

Canvas可以在onPaint中使用Context2D实现自定义绘制.


使用Context2D有几种重要的绘制方法

参照qt绘制canvas示例: https://doc.qt.io/qt-5/qtquick-canvas-example.html

一个简单示例:

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Layouts 1.14
Window {
    visible: true;
    width: 560
    height: 440
    
    Canvas{
        anchors.fill: parent
        id :canvas
        visible: true
        antialiasing: true
        
        onPaint: {
            var ctx = canvas.getContext('2d');
            var originX = 85
            var originY = 75
            ctx.save();
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.translate(originX, originX);
            ctx.strokeStyle = Qt.darker("#b40000", 1.4);
            ctx.fillStyle = "#b40000";
            ctx.lineWidth = 1;
            
            ctx.beginPath();
            ctx.moveTo(75,40);
            ctx.bezierCurveTo(75,37,70,25,50,25);
            ctx.bezierCurveTo(20,25,20,62.5,20,62.5);
            ctx.bezierCurveTo(20,80,40,102,75,120);
            ctx.bezierCurveTo(110,102,130,80,130,62.5);
            ctx.bezierCurveTo(130,62.5,130,25,100,25);
            ctx.bezierCurveTo(85,25,75,37,75,40);
            ctx.closePath();
            ctx.fill();
            if (canvas.stroke)
                ctx.stroke();
            ctx.restore();
        }
        Component.onCompleted: {
            requestPaint()
        }
    }
}


```