
Flickable是qml的滑动窗口

```qml
import QtQuick 2.14
import QtQuick.Window 2.14
import QtQuick.Controls 2.12
import Qt.labs.platform 1.1
import QtQuick.Layouts 1.14
Window {

    visible: true
    width: 640
    height: 480
    title: qsTr("滑动窗口")

    FileDialog{
        id : fileDialog
        acceptLabel : "确定"
        fileMode : FileDialog.OpenFile
        folder : "file:///"
        nameFilters : [
            "JPG(*.jpg)","IMAGE(*.image)","PNG(*.png)"
        ]
        rejectLabel : "拒绝"
        onAccepted: {

        }
    }

    //滑动窗口
    Flickable {
        id : flickView
        anchors.fill: parent
        contentWidth: picture.width
        contentHeight: picture.height
        Image {
            id: picture
            source: fileDialog.file
            asynchronous: true
            onStateChanged: {
                if (picture.state == Image.Error){
                    console.log("加载图片失败 " + picture.source)
                }
            }

        }

        transitions: Transition {
            NumberAnimation { properties: "opacity"; duration: 400 }
        }
        ScrollBar.vertical: ScrollBar{
            id: scroll1
            policy: size<1.0? ScrollBar.AlwaysOn : ScrollBar.AlwaysOff
            contentItem: Rectangle {
                      implicitWidth: 6
                      implicitHeight: 100
                      radius: width / 2
                      color: scroll1.pressed ? "#22aa33" : "#33aa44"
                  }
        }
        ScrollBar.horizontal: ScrollBar{
            id: scroll2
            policy: size<1.0? ScrollBar.AlwaysOn : ScrollBar.AlwaysOff

            contentItem: Rectangle {
                      implicitWidth: 100
                      implicitHeight: 6
                      radius: height / 2
                      color: scroll2.pressed ? "#22aa33" : "#33aa44"
                  }

        }



    }

    //按钮
    Button{

        text: "打开图片"
        onClicked: {
            fileDialog.open()
        }
    }

}

```