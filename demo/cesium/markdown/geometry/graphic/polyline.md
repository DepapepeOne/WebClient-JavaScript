### 绘制线Polyline对象

1. [官方总体介绍链接-Geometry-and-Appearances](https://cesiumjs.org/tutorials/Geometry-and-Appearances/)
2. [官方详细介绍链接-PolylineGeometry](https://cesiumjs.org/Cesium/Build/Documentation/PolylineGeometry.html)
3. [Material样式列表](https://cesiumjs.org/Cesium/Build/Documentation/Material.html)

> PolylineGeometry是Cesium默认的几何对象，该对象的使用相对简单，主要是熟悉各种样式参数，cesium针对每个样式的表达方式可以存在多种描述方式

---

#### 提交BUG
> 找到bug请提交[issue](https://github.com/ParnDeedlit/WebClient-Cesium/issues)

---

> 当你不明白几何图元的使用场景时，就意味着，你只需要使用Entity懒人模式！！！

> 特别注意： 当图元是二维的时候，`geometry`中一般会存在中心点来确定对应的位置。

> 三维的图元，则往往`geometry`中没有中心点信息，其中心点信息是在`modelMatrix`中来确定的
> **GeometryInstance:**
    >>+ `geometry`:{决定几何的大小}
    >>+ attributes:{决定几何的样式}
    >>+ `modelMatrix`:{决定几何的位置}
    >>+  id:"几何的id"

#### 两类描述方式
##### Entity模式--懒人加载模式，这适用于小数据，快速展示，后面的问题多多，最好使用下面的新的模式

~~~ javascript
var redLine = viewer.entities.add({
    name: 'Red line on the surface',
    polyline: {
        positions: Cesium.Cartesian3.fromDegreesArray([-75, 35, -125, 35]),
        width: 5,
        material: Cesium.Color.RED
    }
});
~~~


##### Primitive模式--新加载模式-几何图元，这适用于复杂的定制化场景，核心由三部分组成，几何实例与模型矩阵以及样式

- **PolylineGeometry**

|参数|类型|默认值|描述|
|:---|:---|:---|:---|
|positions|Array.<Cartesian3>||An array of Cartesian3 defining the positions in the polyline as a line strip.线的`几何实体`|
|width|Number|0.0|`可选`The width in pixels.线的`宽度`|
|colors|Array.<Color>||`可选`An Array of Color defining the per vertex or per segment colors.线的`线段颜色`|
|colorsPerVertex|Boolean|`false`|`可选`A boolean that determines whether the colors will be flat across each segment of the line or interpolated across the vertices.控制线的`颜色是否铺平`|
|followSurface|Boolean|`true`|`可选`A boolean that determines whether positions will be adjusted to the surface of the ellipsoid via a great arc.决定线的`路径是否贴合椭球表面`|
|vertexFormat|VertexFormat|VertexFormat.DEFAULT|`可选`The vertex attributes to be computed.线的`顶点计算方式`|
|ellipsoid|Ellipsoid|Ellipsoid.WGS84|`可选`The ellipsoid the circle will be on.线的`地球椭球体`|
|granularity|Number|CesiumMath.RADIANS_PER_DEGREE|`可选`The distance, in radians, between each latitude and longitude. Determines the number of positions in the buffer.线的`精细程度`|



~~~ javascript
 /*
* 新加载模式-几何图元，这适用于复杂的定制化场景，核心由三部分组成，几何实例与模型矩阵以及样式
*/
var polylineinstance = new Cesium.GeometryInstance({
    geometry: new Cesium.PolylineGeometry({
        positions: Cesium.Cartesian3.fromDegreesArrayHeights([
            -75, 55, 500000, 
            -125, 55, 500000
        ]),
        width: 10
    }),
    vertexFormat : Cesium.PolylineMaterialAppearance.VERTEX_FORMAT,
    attributes: {
        color: new Cesium.ColorGeometryInstanceAttribute(1.0, 1.0, 1.0, 1.0)
    },
    id: "polylineinstance"
});

viewer.scene.primitives.add(new Cesium.Primitive({
    geometryInstances: [polylineinstance],
    appearance: new Cesium.PolylineColorAppearance({
        material: Cesium.Material.fromType('Color')
    }) //PolylineColorAppearance/PolylineMaterialAppearance请区分使用场景
}));
~~~

**绑定点击事件**
~~~ javascript
function initPickEvent() {
    var handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);
    handler.setInputAction(function (movement) {
        var pick = viewer.scene.pick(movement.position);
        if (Cesium.defined(pick) && (pick.id === 'polylineinstance')) {
            console.log('Mouse clicked instance.');
        }
    }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
}
~~~



