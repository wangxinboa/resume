# shapefile文件解析

## 简介

&emsp;主要就是使用 javascript 解析 ArcGIS 生成的 shapefile 文件, 然后在网页上使用 three.js 显示出相关图像。比较简单的小项目。

&emsp;在 github 上找到了很多类似的代码, 学习之后再对照官方的技术文档完善。

&emsp;[官网描述](https://docs.fileformat.com/gis/shp/)

&emsp;[官方技术文档](https://www.esri.com/content/dam/esrisites/sitecore-archive/Files/Pdfs/library/whitepapers/pdfs/shapefile.pdf)

&emsp;简单的流程就是先用 arraybuffer 格式读取相关文件; 然后使用 DataView 读取文件数据, 根据官方技术文档分别采用 Int32 、 Float64 提取相关的信息，转化为 js 对象; 最后使用 three.js 根据对应的数据生成模型。

&emsp;整个 arcgis 生成的相关的文件其实不止 shapefile 一个，还有其他配套的文件。 其中 dbf 和 shx 的解析原理与 shp 类似, 只是文件标准和作用有所不同, dbf 用作保存其他自定义的数据, shx 中很多信息与 shp 重复, 用于 shp 数据的回复。 这两者也做了解析。 prj 的话, 用于坐标定位, 需要相关地理坐标知识, 再加上后续没有继续开发的需求, 就暂且放下然后没有然后。

&emsp;这里主要说一下 shapefile 相关的解析过程和模型生成。

## shapefile文件标准

&emsp;shapefile 的文件标准简单来讲如 [Figure 1 Organization of the Main File](https://www.esri.com/content/dam/esrisites/sitecore-archive/Files/Pdfs/library/whitepapers/pdfs/shapefile.pdf#page=7) 所示, 主要是由 Header 部分和 Record 部分组成。

&emsp;Header 部分长度为 100 字节, 主要用于表示文件长度、字节数、版本、形状类型等信息。([Table 1 Description of the Main File Header](https://www.esri.com/content/dam/esrisites/sitecore-archive/Files/Pdfs/library/whitepapers/pdfs/shapefile.pdf#page=8 ))

&emsp;其中形状类型有 Null Shape(0)、Point(1)、PolyLine(3)、Polygon(5)、MultiPoint(8)、PointZ(11)、PolyLineZ(13)、PolygonZ(15)、MultiPointZ(18)、PointM(21)、PolyLineM(23)、PolygonM(25)、MultiPointM(28)、MultiPatch(31) 这些，可以分为空(Null)、点(Point)、线(PolyLine)、多边形面(Polygon)、立体(Patch) 这五大类。(其中的 Z、M, 对应的模型是都生成了, 但是没有深入了解, 也不清楚有什么区别)

&emsp;每个 Record 就是具体描述构建形状的必要信息, 包含 Header 和 Content 两部分, Header 信息保存了 Record 的编号和对应的 Content 长度 (Record 类型已经在 shapefile 的 Header 中记录了), 每个 Content 就是形状信息, 不同的形状就有不同的数据标准。具体的就是去看官方技术文档中的表格描述。

## shapefile模型构建

&emsp;其中点、线的构建较为简单, 不需要多说。 而立体的话, 类型较多, 每个类型都要单独生成模型, 而且也不是很适合平面地图需要, 只简单生成了 TriangleStrip 和 TriangleFan 两个类型 (由于历史久远, 此时我已经忘记这两个类型是什么了 Orz)。比较有技术难度的就是多边形面了, 关键的技术点如下:

&emsp;首先需要判断多边形内一系列点的时钟方向(通过向量计算面积判断), 在官方技术文档中对此有所[说明](https://www.esri.com/content/dam/esrisites/sitecore-archive/Files/Pdfs/library/whitepapers/pdfs/shapefile.pdf#page=13), 顺时针方向的点构成环，逆时针方向的点构成孔洞。

&emsp;判断完之后, 就使用 earcut 算法可以生成对应的多边形面模型。(其实我对算法的理解也是止步于入门水准的实心多边形, 带孔洞的那部分半懂不懂的, 后来由于没有业务相关, 也就没有深入, 毕竟算法是学不完的, 还是要先掌握自己更需要的)

&emsp;earcut 算法的[入门了解视频](https://www.youtube.com/watch?v=QAdfkylpYwc&t=3s)(观看需要有一点点要求, 对技术人员应该不会有什么难度)。

&emsp;[完整的pdf说明](https://www.geometrictools.com/Documentation/TriangulationByEarClipping.pdf)。

&emsp;后来发现，其实 three.js 就是自带了完整的 earcut 算法代码, 然后我就直接调用了(让我欣慰的是 three.js 也是从 mapbox 那里直接复制的, 我也就不客气了), 调整了相关的顶点数据之后, 就直接生成相关的模型了。

## 小结

&emsp;虽然基本的功能完成了, 但是其实还是许多需要补充优化的地方, 最关键的补充就是坐标, 需要与 three.js 做个对应的转化, 不过这个需要相关业务。有点郁闷的是, 写出来之后, 除了小组内看了一下, 就没什么后续了, 或许这就是大公司吧, 资源多得可以去做一些与主要业务不是很相关的尝试。
