qml 创建动画的类

---

**目录**

- [AnchorAnimation:锚点发生改变时动画效果](#anchoranimation锚点发生改变时动画效果)
- [Animator:控件属性的动画效果,该类可以在渲染中运行](#animator控件属性的动画效果该类可以在渲染中运行)
  - [OpacityAnimator:修改控件的透明度](#opacityanimator修改控件的透明度)
  - [RotationAnimator ：旋转控件](#rotationanimator-旋转控件)
  - [ScaleAnimator :缩放控件](#scaleanimator-缩放控件)
  - [UniformAnimator : 修改 uniform 关键字数值](#uniformanimator--修改-uniform-关键字数值)
  - [XAnimator/YAnimator: 修改控件 xy 坐标值](#xanimatoryanimator-修改控件-xy-坐标值)
- [ParallelAnimation: 并行动画效果](#parallelanimation-并行动画效果)
- [ParentAnimation: 父类改变时触发的动画效果](#parentanimation-父类改变时触发的动画效果)
- [PathAnimation: 路径发生改变时发生的动画效果](#pathanimation-路径发生改变时发生的动画效果)
- [PauseAnimation: 短暂暂停动画效果](#pauseanimation-短暂暂停动画效果)
- [PropertyAction/PropertyAnimation: 控件属性动画效果](#propertyactionpropertyanimation-控件属性动画效果)
  - [NumberAnimation: 控件数字属性动画](#numberanimation-控件数字属性动画)
    - [SpringAnimation: 控件模拟弹簧效果](#springanimation-控件模拟弹簧效果)
    - [SmoothedAnimation：控件属性值平滑移动效果](#smoothedanimation控件属性值平滑移动效果)
  - [ColorAnimation:控件颜色变换](#coloranimation控件颜色变换)
  - [RotationAnimation:控件旋转变换](#rotationanimation控件旋转变换)
  - [vertor3dAnimation](#vertor3danimation)
- [ScriptAction: ScriptAction 可用于在动画的特定位置运行脚本](#scriptaction-scriptaction-可用于在动画的特定位置运行脚本)
- [SequentialAnimation : 定义动画效果按照序列执行](#sequentialanimation--定义动画效果按照序列执行)
- [动画示例](#动画示例)
  - [绘制蓝天白云太阳月亮星星一夜又一夜！](#绘制蓝天白云太阳月亮星星一夜又一夜)
  - [弹弹球示例](#弹弹球示例)
  - [绘制路径效果](#绘制路径效果)
  - [绘制小车](#绘制小车)

---

# AnchorAnimation:锚点发生改变时动画效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
Window {
    visible: true;
    width: 560
    height: 440

    Item {
        id: container
        width: 200; height: 200

        Rectangle {
            id: myRect
            width: 100; height: 100
            color: "red"
        }

        states: State {
            name: "reanchored"
            AnchorChanges { target: myRect; anchors.right: container.right }
        }

        transitions: Transition {
            AnchorAnimation { duration: 1000 }
        }

        Component.onCompleted: container.state = "reanchored"
    }

}


```

# Animator:控件属性的动画效果,该类可以在渲染中运行

动画器类型不同于普通的动画类型。当使用 Animator 时，动画可以在渲染线程中运行，当动画完成时，属性值将跳转到结束。

## OpacityAnimator:修改控件的透明度

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480
    color: "#343434"

    Rectangle {
        id: opacityBox
        width: 50
        height: 50
        color: "lightsteelblue"
        OpacityAnimator {
            target: opacityBox;
            from: 0;
            to: 1;
            duration: 5000
            running: true
        }
    }

    Rectangle {
      width: 50
      height: 50
      x: 60
      color: "lightsteelblue"
      OpacityAnimator on opacity{
          from: 0;
          to: 1;
          duration: 1000
      }
  }

}


```

## RotationAnimator ：旋转控件

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id: rotatingBox
        width: 50
        height: 50
        color: "lightsteelblue"
        RotationAnimator {
            target: rotatingBox;
            from: 0;
            to: 360;
            duration: 1000
            running: true
        }
    }

    Rectangle {
        width: 50
        height: 50
        x : 60
        color: "lightsteelblue"
        RotationAnimator on rotation {
            from: 0;
            to: 360;
            duration: 5000
        }
    }

}
```

## ScaleAnimator :缩放控件

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id: scalingBox
        width: 50
        height: 50
        color: "lightsteelblue"
        ScaleAnimator {
            target: scalingBox;
            from: 0.5;
            to: 1;
            duration: 1000
            running: true
        }
    }


    Rectangle {
        width: 50
        height: 50
        x: 60
        color: "red"
        ScaleAnimator on scale {
            from: 0.5;
            to: 1;
            duration: 1000
        }
    }

}
```

## UniformAnimator : 修改 uniform 关键字数值

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    /*
        ShaderEffect是一个顶点着色器
        uniform是一个GLSL shader中的全局常量，，可以随意在任意shader(vertex shader, geometry shader, or fragment shader)访问
        可以使用 UniformAnimator 来动态修改顶点着色器中uniform的值
     */
    ShaderEffect {
        id: shader
        width: 50
        height: 50
        property variant t;
        UniformAnimator {
            target: shader
            uniform: "t"
            from: 0
            to: 1
            duration: 1000
            running: true
        }
        fragmentShader:
        "
            uniform lowp float t;
            varying highp vec2 qt_TexCoord0;
            void main() {
                lowp float c = qt_TexCoord0.y;
                gl_FragColor = vec4(0, 0, c * t, 1);
            }
        "
    }

}
```

## XAnimator/YAnimator: 修改控件 xy 坐标值

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id : rect
        width: 50
        height: 50
        color: "lightsteelblue"
        XAnimator on x {
            from: 100;
            to: 0;
            duration: 1000

        }
        YAnimator
        {
            target: rect
            from: 20
            to :100
            duration: 5000
            running: true
        }
    }

}
```

# ParallelAnimation: 并行动画效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id : rect
        width: 50
        height: 50
        color: "lightsteelblue"
        ParallelAnimation {
            running: true
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "x"
                duration: 1000

            }
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "y"
                duration: 1000
            }

            ColorAnimation {
                target: rect
                property: "color"
                from: "red"
                to: "black"
                duration: 2000
            }
        }
    }

}

```

# ParentAnimation: 父类改变时触发的动画效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Layouts 1.14
Window {
    visible: true;
    width: 560
    height: 440

    Item {
        width: 200; height: 100

        Rectangle {
            id: redRect
            width: 100; height: 100
            color: "red"
        }

        Rectangle {
            id: blueRect
            x: redRect.width
            width: 50; height: 50
            color: "blue"

            states: State {
                name: "reparented"
                ParentChange { target: blueRect; parent: redRect; x: 10; y: 10 }
            }

            transitions: Transition {
                ParentAnimation {
                    NumberAnimation { properties: "x,y"; duration: 1000 }
                }
            }

            MouseArea { anchors.fill: parent; onClicked: blueRect.state = "reparented" }
        }
    }

}
```

# PathAnimation: 路径发生改变时发生的动画效果

demo 参考自 https://blog.csdn.net/quietbxj/article/details/108294697

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Layouts 1.14
Window {
    visible: true;
    width: 560
    height: 440

    Canvas {
        width: 400
        height: 240

        onPaint: {
            //绘制弧线-只是形象来表示控件的运动轨迹
            var ctx=getContext("2d")
            ctx.lineWidth=4
            ctx.strokeStyle="red"
            ctx.arc(200, 0, 160, Math.PI*2, 0, false)
            ctx.stroke()
        }

        Rectangle{
            id:rect
            width: 40
            height: 40
            color: "blue"
            x:20
            y:0

            Component.onCompleted: pathAim.start()

            PathAnimation{
                id:pathAim
                target: rect
                duration: 6000
                //运动控件上的锚定点
                anchorPoint: "20,20"
                //运动策略
                orientation: PathAnimation.TopFirst
                //运动开始/结束与设定值不匹配到匹配的运动周期
                orientationEntryDuration: 200
                orientationExitDuration: 200
                easing.type: Easing.InOutCubic

                path: Path{
                    //起始点
                    startX: 40
                    startY: 0
                    //绘制弧线
                    PathArc{
                        //终点
                        x:360
                        y:0
                        //使用大弧，指定半径
                        useLargeArc: true
                        radiusX: 160
                        radiusY: 160
                        //逆时针方向
                        direction: PathArc.Counterclockwise
                    }
                }
            }
        }
    }

}


```

# PauseAnimation: 短暂暂停动画效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id : rect
        width: 50
        height: 50
        color: "lightsteelblue"
        SequentialAnimation {
            running: true
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "x"
                duration: 1000

            }
            //暂停1秒
            PauseAnimation {
                duration: 1000
            }
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "y"
                duration: 1000
            }

            ColorAnimation {
                target: rect
                property: "color"
                from: "red"
                to: "black"
                duration: 2000
            }
        }
    }

}

```

# PropertyAction/PropertyAnimation: 控件属性动画效果

## NumberAnimation: 控件数字属性动画

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Rectangle {
        id : rect
        width: 50
        height: 50
        color: "lightsteelblue"
        SequentialAnimation {
            running: true
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "x"
                duration: 1000

            }
            PauseAnimation {
                duration: 1000
            }
            NumberAnimation {
                target: rect
                from: 0
                to :100
                property: "y"
                duration: 1000
            }

            ColorAnimation {
                target: rect
                property: "color"
                from: "red"
                to: "black"
                duration: 2000
            }
        }
    }

}
```

### SpringAnimation: 控件模拟弹簧效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 320; height: 480

    Item {
        width: 300; height: 300

        Rectangle {
            id: rect
            width: 50; height: 50
            color: "red"

            //当x，y发生变化时触发
            Behavior on x { SpringAnimation { spring: 2; damping: 0.2 } }
            Behavior on y { SpringAnimation { spring: 2; damping: 0.2 } }
        }

        MouseArea {
            anchors.fill: parent
            onClicked: {
                rect.x = mouse.x - rect.width/2
                rect.y = mouse.y - rect.height/2
            }
        }
    }
}
```

### SmoothedAnimation：控件属性值平滑移动效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Rectangle {
        width: 800; height: 600
        color: "blue"

        Rectangle {
            width: 60; height: 60
            x: rect1.x - 5; y: rect1.y - 5
            color: "green"

            Behavior on x { SmoothedAnimation { velocity: 200 } }
            Behavior on y { SmoothedAnimation { velocity: 200 } }
        }

        Rectangle {
            id: rect1
            width: 50; height: 50
            color: "red"
        }

        focus: true
        Keys.onRightPressed: rect1.x = rect1.x + 100
        Keys.onLeftPressed: rect1.x = rect1.x - 100
        Keys.onUpPressed: rect1.y = rect1.y - 100
        Keys.onDownPressed: rect1.y = rect1.y + 100
    }
}

```

## ColorAnimation:控件颜色变换

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Rectangle {
        width: 100; height: 100
        color: "red"

        ColorAnimation on color { to: "yellow"; duration: 1000 }
    }

}

```

## RotationAnimation:控件旋转变换

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Item {
        width: 300; height: 300

        Rectangle {
            id: rect
            width: 150; height: 100; anchors.centerIn: parent
            color: "red"
            antialiasing: true

            states: State {
                name: "rotated"
                PropertyChanges { target: rect; rotation: 180 }
            }

            transitions: Transition {
                RotationAnimation { duration: 1000; direction: RotationAnimation.Counterclockwise }
            }
        }

        MouseArea { anchors.fill: parent; onClicked: rect.state = "rotated" }
    }
}
```

## vertor3dAnimation

# ScriptAction: ScriptAction 可用于在动画的特定位置运行脚本

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Item {
        width: 400; height: 400

        Rectangle {
            id: rect
            width: 400; height: 400; anchors.centerIn: parent
            color: "red"
            antialiasing: true

            SequentialAnimation {
                running: true
                NumberAnimation {
                    target: rect
                    property: "x"
                    from: 0
                    to:100
                    duration: 1000
                }
                ScriptAction { script: doSomething(); }
                NumberAnimation {
                    target: rect
                    property: "y"
                    from: 0
                    to:100
                    duration: 1000

                }
            }

        }

        MouseArea { anchors.fill: parent; onClicked: rect.state = "rotated" }


    }

    function doSomething(){
        console.log("ScriptAction ....")
    }


}
```

# SequentialAnimation : 定义动画效果按照序列执行

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Item {
        width: 400; height: 400

        Rectangle {
            id: rect
            width: 400; height: 400; anchors.centerIn: parent
            color: "red"
            antialiasing: true

            SequentialAnimation {
                running: true
                NumberAnimation {
                    target: rect
                    property: "x"
                    from: 0
                    to:100
                    duration: 1000
                }
                ScriptAction { script: doSomething(); }
                NumberAnimation {
                    target: rect
                    property: "y"
                    from: 0
                    to:100
                    duration: 1000

                }
            }

        }

        MouseArea { anchors.fill: parent; onClicked: rect.state = "rotated" }


    }

    function doSomething(){
        console.log("ScriptAction ....")
    }


}
```

# 动画示例

## 绘制蓝天白云太阳月亮星星一夜又一夜！

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Particles 2.14
Window {
    visible: true;
    width: 560
    height: 440

    //绘制蓝天
    Rectangle {
        anchors {
            left: parent.left;
            top: parent.top;
            right: parent.right;
            bottom: parent.verticalCenter
        }
        gradient: Gradient {
            GradientStop {
                position: 0.0
                SequentialAnimation on color {
                    //颜色无限循环渐变
                    loops: Animation.Infinite
                    ColorAnimation { from: "#14148c"; to: "#0E1533"; duration: 5000 }
                    ColorAnimation { from: "#0E1533"; to: "#14148c"; duration: 5000 }
                }
            }
            GradientStop {
                position: 1.0
                SequentialAnimation on color {
                    loops: Animation.Infinite
                    ColorAnimation { from: "#14aaff"; to: "#437284"; duration: 5000 }
                    ColorAnimation { from: "#437284"; to: "#14aaff"; duration: 5000 }
                }
            }
        }


    }

    //绘制太阳、月亮、星星
    Item {
        width: parent.width
        height: 2 * parent.height
        NumberAnimation on rotation {
            from: 0
            to: 360
            duration: 10000
            loops: Animation.Infinite
        }
        //太阳
        Image {
            source: "images/sun.png"
            y: 10
            anchors.horizontalCenter: parent.horizontalCenter
            rotation: -3 * parent.rotation
        }
        //月亮
        Image {
            source: "images/moon.png"
            y: parent.height - 74;
            anchors.horizontalCenter: parent.horizontalCenter
            rotation: -parent.rotation
        }
        //ParticleSystem系统生成星星
        ParticleSystem{
            id : p
            x: 0
            y: parent.height/2
            width: parent.width
            height: parent.height
            ImageParticle {
                source: "images/star.png"
                groups: ["star","end"]
                color: "#00333333"
                SequentialAnimation on opacity {
                    loops: Animation.Infinite
                    NumberAnimation { from: 0; to: 1; duration: 5000 }
                    NumberAnimation { from: 1; to: 0; duration: 5000 }
                }
            }
            //发射粒子
            Emitter {
                group: "star"
                anchors.fill: parent
                emitRate: parent.width / 25
                lifeSpan: 5000
            }
        }

    }


    //绘制草坪
    Rectangle{
        anchors.left: parent.left
        anchors.right: parent.right
        anchors.top: parent.verticalCenter
        anchors.bottom: parent.bottom

        gradient: Gradient{
            GradientStop{
                position: 0
                //串行执行动画效果
                SequentialAnimation on color{
                    loops: Animation.Infinite
                    ColorAnimation {
                        from: "#A6E8BE"
                        to: "#A6E8A9"
                        duration: 5000
                    }
                    ColorAnimation {
                        from: "#D3E8A6"
                        to: "#E8D8A6"
                        duration: 5000
                    }
                }
            }

            GradientStop { position: 1.0; color: "#006325" }

        }
    }


}
```

## 弹弹球示例

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Particles 2.14
Window {
    visible: true;
    width: 320; height: 480
    color: "#343434"

    Rectangle{

        id: rect
        anchors.centerIn: parent
        width: 200; height: 200
        radius: 30
        color: "transparent"
        border.width: 4; border.color: "white"
        //上下左右方块
        Repeater{
            model: 4
            Rectangle{
                property var anchorsList: [
                    [parent.verticalCenter,parent.left,"Left"],
                    [parent.verticalCenter,parent.right,"Right"],
                    [parent.top,parent.horizontalCenter,"Top"],
                    [parent.bottom,parent.horizontalCenter,"Bottom"],
                ]
                anchors {
                    verticalCenter: anchorsList[index][0];
                    horizontalCenter: anchorsList[index][1];
                }
                property string text : anchorsList[index][2]
                width: 50
                height: 50
                radius: 25
                color: "#646464"
                border.width: 4; border.color: "white"

                MouseArea{
                    anchors.fill: parent
                    hoverEnabled: true
                    onEntered: {
                        focusRect.x = parent.x;
                        focusRect.y = parent.y;
                        focusRect.text = parent.text;
                    }
                }
            }
        }

        //焦点方块
        Rectangle{
            id : focusRect

            property string text
            x: parent.width /2-25
            y: parent.height /2-25;
            width: 50;
            height: 50
            radius: 25
            border.width: 4; border.color: "white"
            color: "firebrick"

            Behavior on x {
                NumberAnimation {
                    easing.type: Easing.InOutBounce
                    easing.amplitude: 3.0;
                    easing.period: 2.0;
                    duration: 300 }
            }

            Behavior on y {
                NumberAnimation {
                    easing.type: Easing.OutElastic;
                    easing.amplitude: 3.0;
                    easing.period: 2.0;
                    duration: 300 }
            }
            Text {
                id :focusText
                text: focusRect.text
                anchors.centerIn: parent

                color: "white"
                font.pixelSize: 16
                font.bold: true

                //当text发生改变时执行
                Behavior on text{
                    SequentialAnimation{
                        NumberAnimation { target: focusText; property: "opacity"; to: 0; duration: 150 }
                        NumberAnimation { target: focusText; property: "opacity"; to: 1; duration: 150 }
                    }
                }
            }

        }
    }



}


```

## 绘制路径效果

参考自[诺谦](https://blog.csdn.net/qq_37997682/article/details/119590440)

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
Window {
    visible: true;
    width: 800; height: 600

    Canvas {
                id: canvas
                anchors.fill: parent
                antialiasing: true

                onPaint: {
                    var context = canvas.getContext("2d")
                    context.clearRect(0, 0, width, height)
                    context.strokeStyle = "black"
                    context.path = path
                    context.stroke()
                }
            }

            Path {
                id: path
                startX: 100; startY: 100

                PathArc {
                  x: 100; y: 140
                  radiusX: 100; radiusY: 50
                  useLargeArc: true
                  xAxisRotation: 30
                }
    }

}

```

## 绘制小车

**svg 支持的命令如下**

<ul><li>M &#61; moveto,移动画笔到指定点处(只是移动位置,不画线),写法:M x y</li><li>L &#61; lineto,从当前点绘制一条直线到目的点,写法:L x y</li><li>H &#61; horizontal lineto,绘制一条平行线,写法:H x</li><li>V &#61; vertical lineto,绘制一条垂直线,写法:V y</li><li>C &#61; curveto,绘制一条三次贝塞尔曲线,写法:C x1 y1, x2 y2, x y (x和y为终点,其它两个为控制点)</li><li>S &#61; smooth curveto,用来写在C命令后面时来延长贝塞尔曲线,当然也可以跟在S命令后面,写法:S x1 y1, x y(x1和y1是控制点,x和y是终点)</li><li>Q &#61; quadratic Bézier curve,绘制一条二次贝塞尔曲线,写法:Q x1 y1, x y</li><li>T &#61; smooth quadratic Bézier curveto,用来写在Q命令后面时来延长贝塞尔曲线,当然也可以跟在T命令后面,写法:T x y</li><li>A &#61; elliptical Arc,绘制一条圆弧,写法:A rx ry x-axis-rotation large-arc-flag sweep-flag x y
<ul><li>rx、ry:表示圆弧的半径</li><li>x-axis-rotation: 表示x水平方向是否倾斜,比如设置为-45,则圆弧会逆时针倾斜45°,注意:该值仅在x和y半径不同时有用&#xff0c;这意味着圆弧是椭圆形的</li><li>large-arc-flag:设置为0表示绘制小圆弧,为1表示大圆弧</li><li>sweep-flag: 表示弧线的方向&#xff0c;0表示从起点到终点沿逆时针画弧&#xff0c;1表示从起点到终点沿顺时针画弧</li><li>x、y:表示绘制的终点位置</li></ul></li><li>Z &#61; closepath,关闭路径,将会从当前点绘制一条直线到开始点.</li></ul>

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtGraphicalEffects 1.14

Window {
    visible: true;
    width: 800; height: 600

    //实现一个交通灯
    property var timeout: 0

    //绘制背景
    Rectangle{
        anchors.fill: parent

        LinearGradient{
            anchors.fill: parent
            start: Qt.point(0,0)
            end: Qt.point(0, parent.height)
            gradient: Gradient {
                GradientStop { position: 0.0; color: "#A6D7E8" }
                GradientStop { position: 0.78; color: "#A6C0E8" }
                GradientStop { position: 0.79; color: "#58EA66" }
                GradientStop { position: 1.0; color: "#2BF93F" }
            }
        }

    }

    //绘制交通灯
    Canvas{
        id : canvas
        width: parent.width /8
        height: parent.height * 0.58
        anchors.centerIn: parent
        anchors.verticalCenterOffset: (parent.height - parent.height *0.79) /2
        onPaint: {
            var ctx = getContext("2d");
            ctx.clearRect(0, 0, width, height)
            ctx.fillStyle = Qt.rgba(16 /255,18 /255,16 /255, 1);
            ctx.fillRect(0, 0, width, height -90);
            ctx.fillRect(width/2 -15, height -90, 30, 90);

        }
    }


    //绘制红黄绿信号灯
    Column {
        anchors.centerIn: canvas
        anchors.verticalCenterOffset: -45
        spacing: 5

        state: timeout == 1 ? "red_color" :
                              timeout == 2 ?  "yellow_color":  "green_color"


        Rectangle {
            id : redLed
            width: canvas.width *0.70
            height: canvas.width *0.70
            radius: width /2
            antialiasing :true
            color: "red"
            opacity: 0.2
        }

        Rectangle {
            id : yellowLed
            width: canvas.width *0.70
            height: canvas.width *0.70
            radius: width /2
            antialiasing :true
            color: "yellow"
            opacity: 0.2
        }

        Rectangle {
            id : greenLed
            width: canvas.width *0.70
            height: canvas.width *0.70
            radius: width /2
            antialiasing :true
            color: "green"
            opacity: 0.2
        }


        states:[
            State {
                name: "red_color"
                PropertyChanges { target: redLed; opacity: 1 ;}
            },
            State {
                name: "yellow_color"
                PropertyChanges { target: yellowLed; opacity: 1 ; }
            },
            State {
                name: "green_color"
                PropertyChanges { target: greenLed; opacity: 1 ; }
            }
        ]

        //添加转场动画
        transitions: Transition {
                    PropertyAnimation  { target: redLed;  property: "opacity"; duration: 400}
                    PropertyAnimation  { target: yellowLed;  property: "opacity"; duration: 400}
                    PropertyAnimation  { target: greenLed;  property: "opacity"; duration: 400}
        }
    }

    //定时刷新
    Timer{
        interval: 2500;
        running: true;
        repeat: true
        onTriggered: timeout = (timeout+1) %3
    }


}

```