使用Item实现简单的CheckBox

```qml
Item {
    id: root
    implicitHeight: frame.height
    implicitWidth: row.implicitWidth
    width: implicitWidth
    height: implicitHeight
    property alias text: label.text
    property bool checked
    property alias pressed: tapHandler.pressed
    property alias row: row
    signal clicked

    SystemPalette { id: palette }

    Row {
        id: row
        anchors.verticalCenter: parent.verticalCenter
        spacing: 6
        Rectangle {
            id: frame
            gradient: Gradient {
                GradientStop { position: 0.0; color: tapHandler.pressed ? Qt.darker(palette.button, 1.3) : palette.button }
                GradientStop { position: 1.0; color: Qt.darker(palette.button, 1.3) }
            }
            height: label.implicitHeight * 1.5
            width: height
            anchors.margins: 1
            radius: 3
            antialiasing: true
            border.color: Qt.darker(palette.button, 1.5)
            Image {
                id: theX
                source: "images/checkmark.png"
                anchors.fill: frame
                anchors.margins: frame.width / 5
                fillMode: Image.PreserveAspectFit
                smooth: true
                visible: root.checked
            }
        }
        Text {
            id: label
            color: palette.text
            anchors.verticalCenter: frame.verticalCenter
        }
    }
    TapHandler {
        id: tapHandler
        onTapped: {
            parent.checked = !parent.checked
            parent.clicked()
        }
    }
}
```