# 属性

* add : Transition ，添加元素时的过渡动画
* addDisplaced : Transition ,视图内因向视图添加其他项而移位时的过渡动画
* cacheBuffer : int ，此属性决定委托是否保留在视图的可见区域之外
* count : int ，项数量
* currentIndex : int ,当前索引
* currentItem : Item ,当前项
* currentSection : string ，此属性保存当前位于视图开头的部分
* delegate : Component ，当前视图的委托
* displaced : Transition ，添加，删除，移位时的过渡动画
* displayMarginBeginning : int ，
* displayMarginEnd : int
* effectiveLayoutDirection : enumeration ，此属性保存水平面向列表的有效布局方向
* footer : Component ，此属性保存要用作页脚的组件
* footerItem : Item ，它保存了从页脚组件创建的页脚项
* footerPositioning : enumeration，此属性确定页脚项的位置
* header : Component，此属性保存要用作标头的组件
* headerItem : Item，标头的项
* headerPositioning : enumeration，此属性确定标题项的位置。
* highlight : Component，此属性保存要用作高亮显示的组件。
* highlightFollowsCurrentItem : bool，此属性用于显示高亮是否由视图管理。
* highlightItem : Item，它保存了从高亮显示组件创建的高亮显示项。
* highlightMoveDuration : int
* highlightMoveVelocity : real
* highlightRangeMode : enumeration
* highlightResizeDuration : int
* highlightResizeVelocity : real
* keyNavigationEnabled : bool
* keyNavigationWraps : bool
* layoutDirection : enumeration
* model : model
* move : Transition
* moveDisplaced : Transition
* orientation : enumeration
* populate : Transition
* preferredHighlightBegin : real
* preferredHighlightEnd : real
* remove : Transition
* removeDisplaced : Transition
* section
* section.criteria : enumeration
* section.delegate : Component
* section.labelPositioning : enumeration
* section.property : string
* snapMode : enumeration
* spacing : real
* verticalLayoutDirection : enumeration 

# 设计

设计一个listView需要考虑一下方面:

1. 模型(内容数据存储)
2. 委托(场景绘制、颜色主题、数据显示)
3. 动画(进场、删除、添加、平移、过渡等动画)
4. 标头、脚注(提示、标题)
5. 高亮显示

## 模型

model是用来控制listView如何进行数据组织的元素,可以通过实现一个自定义的model来进行数据组织,且这个model可以来自c++类，以此来对数据进行控制。
ListView会从模型中获取组织好的数据并进行显示。例如:

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtGraphicalEffects 1.14

Window {
    visible: true;
    width: 800; height: 600

    ListView{
        id : list
        anchors.fill: parent

        model: ListModel{
            id : model
            onRowsInserted: {
                var tmp =model.get(model.rowCount() -1)
                console.log(tmp.model +"-----------" +tmp.count)
            }
        }

        MouseArea{
            anchors.fill: parent
            onClicked: {
                model.append({"model":"hello world",
                              "count":Math.random() *100
                             })
            }
        }
    }

}

```


## 委托

delegate是用来组织数据如何在Listview中进行显示的元素，delegate通过可以从ListView中获取到model的数据，并按照自定义的格式风格样式在ListView中显示数据。

例如:

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtGraphicalEffects 1.14
import QtQuick.Layouts 1.14
Window {
    visible: true;
    width: 800; height: 600

    ListView{
        id : list
        anchors.fill: parent

        model: ListModel{
            id : model
            onRowsInserted: {
                var tmp =model.get(model.rowCount() -1)
                console.log(tmp.name +"-----------" +tmp.random)
            }
        }

        //自定义绘制委托
        delegate: Rectangle{
            height: 80
            width:  list.width - list.rightMargin - list.leftMargin
            x : list.leftMargin
            y : list.topMargin+ index * 80

            color: "#E1E5E4"

            ColumnLayout{
                anchors.fill: parent
                spacing: 5

                RowLayout{
                    Text {
                        text: name
                        color: "#3D3D3D"
                        font.pixelSize: 17
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        Layout.fillWidth : true
                        Layout.fillHeight: true
                        Layout.preferredHeight: font.pixelSize +6
                        verticalAlignment: Text.AlignBottom
                    }
                    Text {

                        text: {
                            var currentDate = new Date();
                            return currentDate.toLocaleTimeString(Qt.locale(), "hh:mm:ss");
                        }
                        color: "#BABABA"
                        font.pixelSize: 13
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        horizontalAlignment: Text.AlignRight
                        Layout.fillWidth : false
                    }
                }

                Text {
                    text: "random:   " + random
                    color: "#BABABA"
                    font.pixelSize: 13
                    font.family: "Microsoft Yahei"
                    wrapMode: Text.NoWrap
                    verticalAlignment: Text.AlignTop
                    Layout.fillWidth : true
                    Layout.fillHeight: true
                    Layout.preferredHeight: font.pixelSize +6
                    elide: Text.ElideRight
                }
            }
        }


        MouseArea{
            anchors.fill: parent
            onClicked: {
                model.append({"name":"model" + (model.rowCount() +1).toString(),
                              "random":Math.random() *100
                             })
            }
        }
    }

}
```

## 绘制进场、删除、添加、平移、过渡动画

ListView可以修改进场、删除、添加、平移、过渡等动画，用来场景的增加显示效果

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtGraphicalEffects 1.14
import QtQuick.Layouts 1.14
import QtQuick.Controls 2.12
Window {
    visible: true;
    width: 800; height: 600

    Rectangle{
        id :rect
        height: 40
        width: parent.width

        color: "#C0E2DF"

        Text {
            anchors.centerIn: parent
            text: qsTr("ListView demo ")
            font.family: "Microsoft Yahei"
            font.pixelSize: 16
            color: "#FFFFFF"
            horizontalAlignment: Text.AlignHCenter
            verticalAlignment: Text.AlignVCenter
            clip: true
        }

        Button{
            id : add
            width: 40
            text: "添加"
            anchors.right: parent.right
            height: parent.height
            onClicked: {
                amodel.append({"name":"model" + (amodel.rowCount() +1).toString(),
                                  "random":Math.random() *100
                              })
                console.log("click")
            }
        }
    }

    ListView{
        id : list
        x :0
        y:rect.height
        width: parent.width
        height: parent.height- rect.height
        clip: true
        //弹出动画
        populate: Transition {
            NumberAnimation {
                property: "opacity"
                from: 0
                to: 1.0
                duration: 1000
            }
        }
        //添加动画
        add: Transition {
            ParallelAnimation{
                NumberAnimation {
                    property: "opacity"
                    from: 0
                    to: 1.0
                    duration: 1000
                }
                NumberAnimation {
                    properties: "x,y"
                    from: 0
                    duration: 1000
                }
            }
        }
        //移位动画
        displaced: Transition {
            SpringAnimation {
                property: "y"
                spring: 3
                damping: 0.1
                epsilon: 0.25
            }
        }
        //移动动画
        move: Transition {
            NumberAnimation { properties: "x,y"; duration: 1000 }
        }
        //删除动画
        remove: Transition {
            SequentialAnimation{
                NumberAnimation {
                    properties: "y"
                    to: 0
                    duration: 600
                }
                NumberAnimation {
                    property: "opacity"
                    to: 0
                    duration: 400
                }
            }
        }


        Component.onCompleted: {
            amodel.append({"name":"model" + (amodel.rowCount() +1).toString(),
                              "random":Math.random() *100
                          })
        }

        model: ListModel{
            id : amodel
            onRowsInserted: {
                var tmp =amodel.get(amodel.rowCount() -1)
                console.log(tmp.name +"-----------" +tmp.random)
            }
        }

        delegate: Rectangle{
            id : delRect
            property var colseBtnWidth: height + 10
            property var pressX
            property var currentIndex : 0
            height: 80
            width:  list.width - list.rightMargin - list.leftMargin
            x : list.leftMargin
            y : list.topMargin+ index * 80
            border.color: "black"
            border.width: 2

            color: "#E1E5E4"

            ColumnLayout{
                anchors.left: parent.left
                anchors.right: closeBtn.left
                width: parent.width
                spacing: 5
                anchors.margins: 10

                RowLayout{
                    Text {
                        text: name
                        color: "#3D3D3D"
                        font.pixelSize: 17
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        Layout.fillWidth : true
                        Layout.fillHeight: true
                        Layout.preferredHeight: font.pixelSize +6
                        verticalAlignment: Text.AlignBottom
                    }
                    Text {

                        text: {
                            var currentDate = new Date();
                            return currentDate.toLocaleTimeString(Qt.locale(), "hh:mm:ss");
                        }
                        color: "#BABABA"
                        font.pixelSize: 13
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        horizontalAlignment: Text.AlignRight
                        Layout.fillWidth : false
                    }
                }

                Text {
                    text: "random:   " + random
                    color: "#BABABA"
                    font.pixelSize: 13
                    font.family: "Microsoft Yahei"
                    wrapMode: Text.NoWrap
                    verticalAlignment: Text.AlignTop
                    Layout.fillWidth : true
                    Layout.fillHeight: true
                    Layout.preferredHeight: font.pixelSize +6
                    elide: Text.ElideRight
                }
            }


            Button{
                id :closeBtn
                width: 60
                anchors.right: parent.right
                height: parent.height

                background: Rectangle {
                    color: "Transparent"
                    border.color: "black"
                }

                text: "删除"

                contentItem: Text{
                    text: closeBtn.text
                    font.family: "Microsoft Yahei"
                    font.pixelSize: 16
                    color: "#FFFFFF"
                    horizontalAlignment: Text.AlignHCenter
                    verticalAlignment: Text.AlignVCenter
                    clip: true
                }

                onClicked: {
                    amodel.remove(index)
                    console.log("a")
                }

            }

        }



    }

}
```

## 总示例

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtGraphicalEffects 1.14
import QtQuick.Layouts 1.14
import QtQuick.Controls 2.12
Window {
    visible: true;
    width: 800; height: 600

    Rectangle{
        id :rect
        height: 40
        width: parent.width

        color: "#C0E2DF"

        Text {
            anchors.centerIn: parent
            text: qsTr("ListView demo ")
            font.family: "Microsoft Yahei"
            font.pixelSize: 16
            color: "#FFFFFF"
            horizontalAlignment: Text.AlignHCenter
            verticalAlignment: Text.AlignVCenter
            clip: true
        }

        Button{
            id : add
            width: 80
            text: "添加元素"
            anchors.right: parent.right
            height: parent.height
            onClicked: {
                amodel.append({"name":"model" + (amodel.rowCount() +1).toString(),
                                  "random":Math.random() *100
                              })
                console.log("click")
            }
        }
    }

    ListView{
        id : list
        x :0
        y:rect.height
        width: parent.width
        height: parent.height- rect.height
        clip: true

        //标头
        header:Rectangle{
            width: list.width
            height: 20
            border.color: "black"
            Label{
                anchors.centerIn: parent
                text:"this is header"
                font.family: "Microsoft Yahei"
                font.pixelSize: 16
                color:"red"
            }

        }

        //脚注
        footer :Rectangle{
            width: list.width
            height: 20
            border.color: "black"
            Label{
                anchors.right: parent.right
                text:"this is footer"
                font.family: "Microsoft Yahei"
                font.pixelSize: 16
                color:"red"
            }

        }


        //弹出动画
        populate: Transition {
            NumberAnimation {
                property: "opacity"
                from: 0
                to: 1.0
                duration: 1000
            }
        }
        //添加动画
        add: Transition {
            ParallelAnimation{
                NumberAnimation {
                    property: "opacity"
                    from: 0
                    to: 1.0
                    duration: 1000
                }
                NumberAnimation {
                    properties: "x,y"
                    from: 0
                    duration: 1000
                }
            }
        }
        //移位动画
        displaced: Transition {
            SpringAnimation {
                property: "y"
                spring: 3
                damping: 0.1
                epsilon: 0.25
            }
        }
        //移动动画
        move: Transition {
            NumberAnimation { properties: "x,y"; duration: 1000 }
        }
        //删除动画
        remove: Transition {
            SequentialAnimation{
                NumberAnimation {
                    properties: "x,y"
                    to: 0
                    duration: 600
                }
                NumberAnimation {
                    property: "opacity"
                    to: 0
                    duration: 1000
                }
            }
        }


        Component.onCompleted: {
            amodel.append({"name":"model" + (amodel.rowCount() +1).toString(),
                              "random":Math.random() *100
                          })
        }

        model: ListModel{
            id : amodel
            onRowsInserted: {
                var tmp =amodel.get(amodel.rowCount() -1)
                console.log(tmp.name +"-----------" +tmp.random)
            }
        }

        delegate: Rectangle{
            id : delRect
            property var colseBtnWidth: height + 10
            property var pressX
            property var currentIndex : 0
            height: 80
            width:  list.width - list.rightMargin - list.leftMargin
            x : list.leftMargin
            y : list.topMargin+ index * 80
            border.color: "black"
            border.width: 2

            color: "#E1E5E4"

            ColumnLayout{
                anchors.left: parent.left
                anchors.right: closeBtn.left
                width: parent.width
                spacing: 5
                anchors.margins: 10

                RowLayout{
                    Text {
                        text: name
                        color: "#3D3D3D"
                        font.pixelSize: 17
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        Layout.fillWidth : true
                        Layout.fillHeight: true
                        Layout.preferredHeight: font.pixelSize +6
                        verticalAlignment: Text.AlignBottom
                    }
                    Text {

                        text: {
                            var currentDate = new Date();
                            return currentDate.toLocaleTimeString(Qt.locale(), "hh:mm:ss");
                        }
                        color: "#BABABA"
                        font.pixelSize: 13
                        font.family: "Microsoft Yahei"
                        wrapMode: Text.WrapAtWordBoundaryOrAnywhere
                        horizontalAlignment: Text.AlignRight
                        Layout.fillWidth : false
                    }
                }

                Text {
                    text: "random:   " + random
                    color: "#BABABA"
                    font.pixelSize: 13
                    font.family: "Microsoft Yahei"
                    wrapMode: Text.NoWrap
                    verticalAlignment: Text.AlignTop
                    Layout.fillWidth : true
                    Layout.fillHeight: true
                    Layout.preferredHeight: font.pixelSize +6
                    elide: Text.ElideRight
                }
            }


            Button{
                id :closeBtn
                width: 60
                anchors.right: parent.right
                height: parent.height

                background: Rectangle {
                    color: "Transparent"
                    border.color: "black"
                }

                text: "删除"

                contentItem: Text{
                    text: closeBtn.text
                    font.family: "Microsoft Yahei"
                    font.pixelSize: 16
                    color: "#FFFFFF"
                    horizontalAlignment: Text.AlignHCenter
                    verticalAlignment: Text.AlignVCenter
                    clip: true
                }

                onClicked: {
                    amodel.remove(index)
                    console.log("a")
                }

            }

        }



    }

}

```