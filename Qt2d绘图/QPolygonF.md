# QPolygonF

## 函数

### 构造函数

1. `QPolygonF()`
2. `QPolygonF(int size)`
3. `QPolygonF(const QVector<QPointF> &points)`
4. `QPolygonF(QVector<QPointF> &&v)`
5. `QPolygonF(const QRectF &rectangle)`
6. `QPolygonF(const QPolygon &polygon)`
7. `QPolygonF(const QPolygonF &polygon)`
8. `QPolygonF(QPolygonF &&other)`

### 普通函数

1. 平移所有的点
    1. `void translate(const QPointF &offset)`
    2. `void translate(qreal dx,qreal dy)`
    3. `QPolygonF translated(const QPointF $offset)`