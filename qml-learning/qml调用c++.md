
qml可以调用c++类：

* qml模块提供了一组api，用来将c++类拓展到qml中
* 可以通过添加c++类来拓展现有的QML类型，用于调用qml无法直接调用的c++函数
  

# 创建c++类

qml使用了qt的元对象系统来进行交互，c++类必须满足以下条件:

* 继承于QObject类
* 类必须声明Q_OBJECT宏

下面是表盘控件到qml示例

**cutomqmlplot.h**

```cpp
#ifndef CUTOMQMLPLOT_H
#define CUTOMQMLPLOT_H

#include <QQuickPaintedItem>

class CutomQmlPlot : public QQuickPaintedItem
{
    Q_OBJECT
    Q_PROPERTY(qreal radius READ getRadius WRITE setRadius )

public:
    explicit CutomQmlPlot(QQuickItem *parent = nullptr);

    qreal getRadius() const {return radius;}
    void setRadius(qreal radius) { this->radius =radius; }


    void paint(QPainter *painter) override;

    void drawOSDial(QPainter &p);
    void drawXY(QPainter &p);
    void drawIDial(QPainter &p);
    void drawScale(QPainter &p);
    void drawScaleText(QPainter &p);
    void drawCenterDial(QPainter &p);
    void drawSlideScale(QPainter &p);
    void drawSlideScale2(QPainter &p);
    void drawShape(QPainter &p);
    void drawPoint(QPainter &p);
    void drawCenterBk(QPainter &p);
    void drawbkColor(QPainter &p);
    void drawIconValue(QPainter &p);
    void drawLabel(QPainter &p);
    void drawDialShade(QPainter &p,qreal start, qreal end,qreal ratio,int alpha);

    Q_INVOKABLE void clear(){
        this->setVisible(false);
    }
private:
    qreal radius; //圆半径

};

#endif // CUTOMQMLPLOT_H
```

**cutomqmlplot.cpp**

```cpp
#include "cutomqmlplot.h"
#include <QPainter>
#include <QConicalGradient>
#include <qmath.h>
CutomQmlPlot::CutomQmlPlot(QQuickItem *parent) :QQuickPaintedItem(parent)
{

}
/**
 * @brief 绘制外圆
 * @param p
 */
void CutomQmlPlot::drawOSDial(QPainter &p)
{
    static QColor OSColor(qRgb(252,252,248));

    p.save();
    p.setPen(Qt::NoPen);
    //设置外圆渐变色，表盘渐变分为7个位置
    QConicalGradient conical(0,0,90);
    conical.setColorAt(0,OSColor);
    conical.setColorAt(1.0,OSColor);

    conical.setColorAt(0.125,OSColor.darker(80));
    conical.setColorAt(0.375,OSColor.darker(50));
    conical.setColorAt(0.625,OSColor.darker(50));
    conical.setColorAt(0.875,OSColor.darker(80));

    conical.setColorAt(0.25,OSColor.darker(130));
    conical.setColorAt(0.75,OSColor.darker(130));

    p.setBrush(conical);
    p.drawEllipse(QPointF(0,0),radius * 0.98,radius * 0.98);
    //绘制另外一个圆

    conical.setAngle(45);

    conical.setColorAt(0,OSColor.darker(130));
    conical.setColorAt(0.5,OSColor.darker(130));

    conical.setColorAt(0.12,OSColor.darker(80));
    conical.setColorAt(0.88,OSColor.darker(80));

    conical.setColorAt(0.4,OSColor.darker(50).darker(130));
    conical.setColorAt(0.6,OSColor.darker(50));


    conical.setColorAt(0.25,OSColor.darker(130));

    conical.setColorAt(0.75,OSColor.darker(130));
    conical.setColorAt(1,OSColor.darker(130));

    p.setBrush(conical);
    p.drawEllipse(QPointF(0,0),radius * 0.94,radius * 0.94);

    p.restore();
}
/**
 * @brief 绘制xy坐标轴，以便调试
 * @param p
 */
void CutomQmlPlot::drawXY(QPainter &p)
{
    p.save();
    p.setPen(QPen(Qt::black,2));
    QPointF arrow[3] ={
        QPointF(radius,10),
        QPointF(radius +10,0),
        QPointF(radius,-10),
    };

    p.drawLine(0,0,0,radius);
    p.drawLine(0,0,radius,0);
    p.drawPolygon(arrow,3);
    p.rotate(90);
    p.drawPolygon(arrow,3);

    p.restore();
}

void CutomQmlPlot::drawIDial(QPainter &p)
{
    static QColor  IBk(qRgb(26,26,26));
    p.save();
    p.setBrush(IBk);

    p.drawEllipse(QPointF(0,0),radius * 0.90,radius * 0.90);

    p.restore();
}

/**
 * @brief 绘制刻度尺
 * @param p
 */
void CutomQmlPlot::drawScale(QPainter &p)
{
    //大刻度
    const QPointF bigScale[4] ={
        QPointF(radius *0.90,-2.5),
        QPointF(radius *0.77,-2.5),
        QPointF(radius *0.77,1.5),
        QPointF(radius *0.90,1.5)
    };
    //小刻度
    const QPointF smallScale[4] ={
        QPointF(radius *0.90,-2),
        QPointF(radius *0.81,-2),
        QPointF(radius *0.81,1),
        QPointF(radius *0.90,1)
    };

    p.save();
    p.setBrush(Qt::white);
    p.rotate(180);

    for (int i =0;i <=24;++i) {
        if(i % 4 == 0){
            p.drawPolygon(bigScale,4);
        }else{
            p.drawPolygon(smallScale,4);
        }
        p.rotate(7.5);
    }

    p.restore();

}
/**
 * @brief 绘制刻度对应的数值 0- 100 ,如果使用rotate,会导致字体选择，所以选择计算坐标点绘制
 * @param p
 */
void CutomQmlPlot::drawScaleText(QPainter &p)
{
    //对齐方式
    const int alingns[7] ={
        Qt::AlignVCenter|Qt::AlignTop,
        Qt::AlignLeft|Qt::AlignHCenter,
        Qt::AlignLeft|Qt::AlignTop,
        Qt::AlignLeft|Qt::AlignHCenter,
        Qt::AlignLeft|Qt::AlignTop,
        Qt::AlignLeft|Qt::AlignHCenter,
        Qt::AlignVCenter|Qt::AlignLeft
    };

    p.save();
    QFont   font;
    int size =20;
    font.setFamily("Euphemia");
    font.setPixelSize(size*1.15);
    font.setWeight(QFont::DemiBold);
    p.setFont(font);

    //    drawXY(p);
    p.setPen(Qt::white);
    QPointF textPos(radius * 0.73,0);
    qreal len = qSqrt(textPos.x() * textPos.x() + textPos.y() + textPos.y()) ;
    for (int i =0;i <=6;++i) {
        //计算坐标
        textPos = [&](QPointF point,qreal ange) -> QPointF{
            const qreal PI = 3.14159265;
            qreal arc = ange / 180 * PI;
            point.setX(len * qCos(arc));
            point.setY(len * qSin(arc));
            return point;
        }(textPos,180 + 30*(i));
        p.drawText([&]()->QRectF{
                       //计算偏差值
                       const QPointF offest[7]={
                           QPointF(0,20),
                           QPointF(10,5),
                           QPointF(10,5),
                           QPointF(20,0),
                           QPointF(20,5),
                           QPointF(40,5),
                           QPointF(40,20)
                       };
                       return  QRectF(textPos -offest[i] ,QSize(size *2,size *2)) ;

                   }(),alingns[i],QString::number(20 * i));
    }

    p.restore();
}

void CutomQmlPlot::drawCenterDial(QPainter &p)
{
    p.save();

    p.setBrush(Qt::gray);
    p.drawEllipse(QPointF(0,0),radius*0.38,radius*0.38);

    p.restore();
}

void CutomQmlPlot::drawSlideScale(QPainter &p)
{
    QColor slide(83,213,251);

    p.save();

    //计算绘制比例，使用辐射渐变形成颜色效果
    //绘制分为3部分
    //1、弧形结束点到刻度顶点
    //2、大刻度点绘制出弧形
    //3、圆心到大刻度点
    //计算渐变颜色的比例
    //  g3 g2  g1
    //  (---(--(---- 圆心
    //g1->中心圆渐变到大刻度位置
    //g2->大刻度渐变到刻度内的三分之一
    //g3->刻度内的三分之一渐变到刻度结束位置
    qreal g1 = ((qreal)radius*0.77)/((qreal)radius*0.90);
    qreal g2 = g1 + (1-g1) /3 +0.01;
    qreal g3 =1- (1-g1) /3;

    QColor Tint_SlideColor =slide;

    QRadialGradient radial(0,0,radius * 0.90);


    Tint_SlideColor.setAlpha(40);
    radial.setColorAt(1,Tint_SlideColor);
    radial.setColorAt(g1 -0.005,Tint_SlideColor);
    radial.setColorAt(0,Qt::transparent);
    radial.setColorAt(g1 -0.006,Qt::transparent);

    Tint_SlideColor = slide;
    Tint_SlideColor.setAlpha(50);
    radial.setColorAt(g3-0.03,Tint_SlideColor);
    radial.setColorAt(g2+0.03,Tint_SlideColor);

    Tint_SlideColor = slide;
    Tint_SlideColor.setAlpha(50);
    radial.setColorAt(g3-0.01,Tint_SlideColor.darker(200));
    radial.setColorAt(g2+0.01,Tint_SlideColor.darker(200));

    radial.setColorAt(g3,Tint_SlideColor);
    radial.setColorAt(g2,Tint_SlideColor);

    QRectF rectangle(-radius *0.90 , - radius*0.90 , radius*1.80, radius*1.80);
    int startAngle = 180 * 16;
    int spanAngle = -180 * 16;
    p.setBrush(radial);
    p.drawPie(rectangle, startAngle, spanAngle);
    p.restore();
}
/**
 * @brief 绘制划过渐变效果
 * @param p
 */
void CutomQmlPlot::drawSlideScale2(QPainter &p)
{
    p.save();

    //每40度一个梯级颜色，形成一个渐变效果
    //需要绘制三个pie
    QColor slides[3] ={
        QColor(45,0,255),
        QColor(0,233,255),
        QColor(45,255,0)
    };

    const qreal angles[3] ={
        60 / 180,
        120/ 180,
        180/ 180
    };


    //使用锥形渐变
    QConicalGradient conical(0,0,-90);

    for (int i =0;i <3 ;++i) {
        conical.setColorAt(angles[i],slides[i]);
    }
    p.setPen(Qt::NoPen);
    p.setBrush(conical);
    qreal top=0.8666668;
    qreal bottom=0.8223336;


    int startAngle = 180 * 16;
    int spanAngle = -180 * 16;
    p.drawPie(QRectF((qreal)-radius*top,(qreal)-radius*top,(qreal)radius*top*2,
                     (qreal)radius*top*2),startAngle,spanAngle);

    p.setBrush(QColor(26,26,26));
    p.drawPie(QRectF((qreal)-radius*bottom,(qreal)-radius*bottom,
                     (qreal)radius*bottom*2,(qreal)radius*bottom*2),startAngle,spanAngle);
    /*绘制浅渐变*/

    for(int i=0; i< 3;i++)
        slides[i].setAlpha(90);

    for(int i =0 ; i<3; i++)
        conical.setColorAt(angles[i],slides[i]);


    p.setBrush(conical);
    p.drawPie(QRectF((qreal)-radius*0.90,(qreal)-radius*0.90,
                     (qreal)radius*1.80,(qreal)radius*1.80),
              startAngle,spanAngle);




    p.restore();
}

void CutomQmlPlot::drawShape(QPainter &p)
{
    p.save();
    int PenWidth = radius*0.90-radius*0.77+4;


    p.setPen(QPen(QBrush(QColor(255,255,255,30)),PenWidth));
    p.setBrush(Qt::transparent);


    p.drawArc(QRectF(-radius*0.90+PenWidth/2-3,-radius*0.90+PenWidth/2-3,
                     radius*1.80-PenWidth+3,radius*1.80-PenWidth+3)
              ,180 *16,-180 *16);

    p.restore();
}
/**
 * @brief 绘制指针
 * @param p
 */
void CutomQmlPlot::drawPoint(QPainter &p)
{
    p.save();
    QColor PointerColor=QColor(249,2,1);

    QPainterPath path;
    path.setFillRule(Qt::WindingFill);
    path.moveTo(4,radius *0.9 *0.38);
    path.lineTo(-4,radius *0.9 *0.38);
    path.lineTo(-2,radius *0.70);
    path.lineTo(2,radius *0.70);
    path.addEllipse(QPointF(0,radius *0.72 ),radius * 0.02,radius * 0.02);

    p.setBrush(PointerColor);
    p.setPen(PointerColor.darker(150));
    p.rotate(270);


    p.drawPath(path);

    p.restore();
}

void CutomQmlPlot::drawCenterBk(QPainter &p)
{
    p.save();
    QColor centercolor(220,220,220);
    QRadialGradient Radial(0,0,radius *0.38,0,0);

    Radial.setColorAt(1,centercolor.lighter(130));
    Radial.setColorAt(0.96,centercolor.lighter(110));
    Radial.setColorAt(0.95,centercolor.lighter(105));
    Radial.setColorAt(0.80,centercolor);

    p.setBrush(Radial);
    p.drawEllipse(QPointF(0,0), radius *0.38,radius *0.38);

    p.restore();
}

void CutomQmlPlot::drawbkColor(QPainter &p)
{
    p.save();
    p.setPen(Qt::NoPen);
    QColor bkColor(26,26,26);
    p.setBrush(bkColor);
    p.drawEllipse(QPointF(0,0), radius*0.77-2,radius*0.77-2);

    p.setBrush(bkColor.darker(200));

    QRadialGradient Radial(0,0,radius *0.38 *1.06,0,0);

    Radial.setColorAt(1,bkColor);
    Radial.setColorAt(0.98,bkColor.darker(100));
    Radial.setColorAt(0.95,bkColor.darker(110));
    Radial.setColorAt(0.90,bkColor.darker(120));

    p.setBrush(Radial);

    p.drawEllipse(QPointF(0,0),radius *0.38 *1.18,radius *0.38 *1.18);
    p.restore();
}

void CutomQmlPlot::drawIconValue(QPainter &p)
{
    p.save();

    //计算图标位置
    QPainterPath path;
    path.moveTo(6,-radius * 0.26);
    path.lineTo(-12,-radius * 0.16);
    path.lineTo(-2,-radius * 0.16);
    path.lineTo(-6,-radius * 0.08);
    path.lineTo(8,-radius * 0.18);
    path.lineTo(-2,-radius * 0.18);
    path.closeSubpath();
    p.setBrush(Qt::yellow);
    p.drawPath(path);

    //计算字体大小，图标位置
    qreal fontSize = 40;
    QFont font = p.font();
    font.setFamily("DokChampa");
    font.setPointSizeF(fontSize);

    p.setFont(font);
    p.drawText(QRectF(-radius *0.30,-radius *0.15,radius *0.30 *2,fontSize *2)
               ,Qt::AlignCenter,"120V");

    p.restore();
}

void CutomQmlPlot::drawLabel(QPainter &p)
{
    qreal ValueSize=radius *0.38/4;

    p.save();

    p.setFont(QFont("Euphemia",ValueSize,QFont::Normal));
    p.setPen(Qt::white);

    p.drawText(QRectF(-ValueSize*6,radius *0.38*1.2,ValueSize*12,ValueSize*2)
               ,Qt::AlignCenter,"电压表") ;

    p.restore();
}

void CutomQmlPlot::drawDialShade(QPainter &p, qreal start, qreal end, qreal ratio, int alpha)
{

}

void CutomQmlPlot::paint(QPainter *painter){
    painter->setRenderHint(QPainter::Antialiasing,true);
    painter->setRenderHint(QPainter::SmoothPixmapTransform);

    //计算半径
    painter->translate(width() /2 ,height() /2);
    radius = qMin(width(),height())/2 -40;
    QPainter &p = *painter;
    drawOSDial(p);
    drawIDial(p);
    drawSlideScale2(p);
    drawShape(p);
    drawScale(p);
    drawbkColor(p);
    drawCenterDial(p);
    drawScaleText(p);
    drawPoint(p);
    drawCenterBk(p);
    drawIconValue(p);
    drawLabel(p);
}

//主函数中需要注册类型
#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include "cutomqmlplot.h"
int main(int argc, char *argv[])
{
    QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);

    QGuiApplication app(argc, argv);

    //注册qml类型 import plot 1.0 控件名称 CutomQmlPlot
    qmlRegisterType<CutomQmlPlot>("plot",1,0,"CutomQmlPlot");

    QQmlApplicationEngine engine;
    const QUrl url(QStringLiteral("qrc:/main.qml"));
    QObject::connect(&engine, &QQmlApplicationEngine::objectCreated,
                     &app, [url](QObject *obj, const QUrl &objUrl) {
        if (!obj && url == objUrl)
            QCoreApplication::exit(-1);
    }, Qt::QueuedConnection);
    engine.load(url);

    return app.exec();
}

```

**qml中调用**

```qml
import QtQuick 2.14
import QtQuick.Window 2.0
import plot 1.0

Window {
    visible: true;
    width: 800; height: 600

    CutomQmlPlot{
        anchors.fill: parent

        MouseArea{
            anchors.fill: parent

            onClicked: {
                parent.clear()
            }
        }
    }

}
```


在上述代码中有两个重要的类型 :

* QQuickItem : qt quick 中所有可视项都继承自QQuickItem,他定义了最基本的属性，如锚点，大小。坐标等
* QQuickPaintedItem ：继承自QQuickItem，并提供了paint函数用于在c++中实现绘制
* Q_PROPERTY : 定义了元对象数据，会在qml中生成对应的属性

```cpp
Q_PROPERTY(type name
           (READ getFunction [WRITE setFunction] |
            MEMBER memberName [(READ getFunction | WRITE setFunction)])
           [RESET resetFunction]
           [NOTIFY notifySignal]
           [REVISION int]
           [DESIGNABLE bool]
           [SCRIPTABLE bool]
           [STORED bool]
           [USER bool]
           [CONSTANT]
           [FINAL])

```

* Q_INVOKABLE : 声明此宏在c++函数前面后，qml可以直接使用此函数