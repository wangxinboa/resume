# dxf文件解析

## 简介

&emsp;主要就是使用 javascript 解析 autoCAD 生成的 dxf 文件, 然后在网页上使用 three.js 显示出相关图像。 比较简单的小项目。与 shapefile 一样, 差不多完成之后, 被打入冷宫, 无人问津。(当时我还问了, 感觉这玩意做了好像没什么用, 和我说是会遇到一些图纸, 结果是真的没什么用)

&emsp;与 shapefile 差不多, 在 github 上找到了很多类似的代码, 学习之后再对照官方的技术文档完善。

&emsp;[文档说明](https://knowledge.autodesk.com/zh-hans/support/autocad/learn-explore/caas/downloads/downloads/CHS/content/download-install-autocad-2021-product-help.html)

&emsp;在这里需要感谢一下 [dxf-viewer](https://github.com/vagran/dxf-viewer), 让我~~抄袭~~借鉴了不少模型生成的代码。

## 解析和模型生成

&emsp;流程和 shapefile 差不多, 由两步组成, 解析文件和生成模型, 不过由于文件的格式不同, 解析方式也不同, dxf 相对于 shp 臃肿冗余了不少(通过文档说明也能看出来, 一个是 pdf 说明, 一个是需要下载安装的网站说明, 完全是不是一个级别的体量)。

&emsp;dxf 是直接以 text 的文件格式读取的, 里面的信息按行排列, 有点类似于将树结构拍扁转化为数组。主要是七大类 HEADER 、CLASSES 、TABLES 、BLOCKS 、ENTITIES 、OBJECTS 、THUMBNAILIMAGE, 用于生成模型需要的数据主要是 TABLES 、BLOCKS 、ENTITIES 三种, 其他的基本可以忽视。具体将 dxf 解析为 js 对象 的过程可以参考 [dxf-parser](https://github.com/gdsestimating/dxf-parser) (感觉有点难以用语言描述)

&emsp;以下是我记得的三大类信息(可能有些地方说的不对, 有些久, 也不是主要业务, 官方文档也不好, 记不太清了)

### ENTITIES

&emsp;其中 ENTITIES 里面包含了最基本的图形元素图元, 类似于 shapefile 里的各类形状(点、线、面、立体), 不过不同的是, autoCAD 包含了四十几种图元(大概吧, 很久没关注, 也记不太清有多少了), 其中我分为基本图元(点、线、面、弧、虚线、圆等等)和包含基本的图元的 Insert (我并没有完全解析完, 描述可能不对请多包涵, 想要详细信息的话, 可以下载文档说明, 打开本地网站去了解)。

### TABLES

&emsp;每个基本图元都有一个属于自己的图层名, 表明自己所在的图层。 可以通过操作图层直接操作特定的批量图元(感觉这个和 photoshop 类似), 图元可以根据具体情况, 获取颜色(是自己的颜色还是图层的颜色), 也可以根据图层是否冻结的属性决定是否显示, 图层信息保存在 TABLES 中。

### BLOCKS

&emsp;每个 Insert 图元其实就是调用了 BLOCKS 里的 block 信息, 每个 block 就是保存了多个基本图元的模版, 不过需要根据每个 Insert 调节基本位置 、旋转角度等信息。

## 小结

&emsp;简单来说, 就是这样(图元数量太多就不具体一一描述了), 不过具体写下来就有很多问题, 比如说文字的实现，一些图元之间的复杂关系等等。项目体验和 shapefile 完全不一样, 如果说 shapefile 的文件标准设计的精致小巧的话, 那么 dxf 就是臃肿庞大, 感觉部分图元标准是有些重复的。

&emsp;严格地说, 这个解析工具只能算是初稿, 我只完成了部分的图元实现, 不过后来有别的事情, 就没有然后了。

&emsp;与 shapefile 类似的结局, 一起去冷宫作伴。
