
```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import QtQuick.Controls 2.4
import QtQuick.Layouts 1.14
import Qt.labs.platform 1.1
Window {
    visible: true;
    width: 560
    height: 440

    property string defaltFolderUrl: "file:///C:/"     // 对话框目录路径

    FileDialog {
        id: fileDialog
        acceptLabel: "确定"  
        rejectLabel: "取消"
        nameFilters: ["All (*)", "Text files (*.txt)", "HTML files (*.png *.jpg)"]
        folder: defaltFolderUrl
        onAccepted: {
            textArea.text = "当前路径:\n  "+defaltFolderUrl + "\n\n" + title + ":\n"
            for (var i in files) {
                textArea.text += "  " + files[i] + "\n"
            }
        }
        onFolderChanged: {
             defaltFolderUrl = folder;
        }
    }

    FolderDialog {
        id: folderDlialog
        acceptLabel: "确定"
        rejectLabel: "取消"
        folder: defaltFolderUrl
        options: FolderDialog.ShowDirsOnly
        onAccepted: {
            textArea.text = "当前路径:\n  "+defaltFolderUrl + "\n\n" + title + ":\n  "
            textArea.text += currentFolder
            defaltFolderUrl = currentFolder
        }
        onFolderChanged: {
             defaltFolderUrl = folder;
        }
    }

    RowLayout {
        anchors.fill: parent
        anchors.margins: 20
        spacing: 10


        ScrollView {
              id: view
              Layout.fillWidth: true
              Layout.fillHeight: true
              Layout.columnSpan: 3
              Layout.preferredWidth: 240
              Layout.preferredHeight: 300
              clip: true
              ScrollBar.vertical.policy: textArea.contentHeight > Layout.preferredHeight ?
                                             ScrollBar.AlwaysOn : ScrollBar.AlwaysOff;  // 如果文本内容高度大于显示高度,则一直显示垂直滑动条

              TextArea {
                id: textArea
                padding: 4
                implicitWidth: 240
                wrapMode: TextArea.WrapAnywhere
                text: "当前路径:\n  "+defaltFolderUrl
                font.pixelSize: 14
                background: Rectangle {
                      width: parent.width
                      height: parent.height
                      border.color: "#B0B0B0"
                      radius: 3
                }

              }
        }

        GroupBox {
             title: "请选择对话框"
             Layout.fillHeight: true
             Layout.fillWidth: false
             Layout.preferredWidth: 130  // 在GridLayout中要想固定指定宽度,必须使用preferredWidth,然后将fillWidth置为false
             Layout.preferredHeight: 300
             Column {
                 anchors.fill: parent
                 spacing: 12
                 Button {
                     text: "打开单个文件"
                     onPressed: {
                        fileDialog.title = text
                        fileDialog.fileMode = FileDialog.OpenFile
                        fileDialog.open()
                     }
                 }
                 Button {
                     text: "打开多个文件"
                     onPressed: {
                        fileDialog.title = text
                        fileDialog.fileMode = FileDialog.OpenFiles
                        fileDialog.open()
                     }

                 }
                 Button {
                     text: "保存文件"
                     onPressed: {
                        fileDialog.title = text
                        fileDialog.fileMode = FileDialog.SaveFile
                        fileDialog.currentFile = "file:///123.txt"
                        fileDialog.open()
                     }

                 }
                 Button {
                     text: "选择文件夹"
                     onPressed: {
                        folderDlialog.title = text
                        folderDlialog.open()
                     }

                 }
             }
        }
    }

}
```