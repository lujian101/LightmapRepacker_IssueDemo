# LightmapRepacker_IssueDemo
unity lightmap texture re-packing issue demo

演示一个Lightmap纹理在拆分重组的时候，因一些误差导致的纹理坐标偏差，一些计算方法都是自创，所以并不保证正确。

Demo演示步骤：
1. 先打开Scene/test.unity这个测试场景
2. 选中Models节点
3. 执行Tools/LightmapRepacker/Repack For Selected GOs， lightmap重组，此场景的lightmap，和涉及模型都会重新计算uv，并更新回所有的Renderer。
4. 如果要再次测试，请**不要保存结果**，直接重新载入一次场景，再处理。

拆分之前：
![](https://github.com/lujian101/LightmapRepacker_IssueDemo/blob/master/LightmapRepacker/pictures/1.png?raw=true)


拆分之后：
![](https://github.com/lujian101/LightmapRepacker_IssueDemo/blob/master/LightmapRepacker/pictures/2.png?raw=true)


从上面两张图来看，虽然差异很小，但是如果原始的Lightmap块很小，那么很小的偏差也会带来非常大的纹理坐标误差。同样误差0.001在尺寸在纹理采样范围较大的块，几乎看不出来，但是如果本来你的纹理块就非常小，那么即使你的绝对误差很小，但实际上你采样到的纹理偏差就非常大了。在我例子中，没有刻意为了去修补那几个像素的偏移硬编码一些诡异代码，而是直接采用公式计算出来的结果。既然，结果不正确，所以我们寻求完美的计算方法。

在拆分lightmap这个示例中，可能有几个因素会影响最终效果：
1. 原始模型的uv展开，有两种方式：美术手工展开（一般会要求他们预留一些包边），Unity导入时自动展开（这里面会自动添加一些包边）

	![](https://github.com/lujian101/LightmapRepacker_IssueDemo/blob/master/LightmapRepacker/pictures/3.png?raw=true)
2. Lightmap烘焙时，设置各个图块之间的间隔
	![](https://github.com/lujian101/LightmapRepacker_IssueDemo/blob/master/LightmapRepacker/pictures/4.png?raw=true)
    经过验证，这个值会最终表现在lightmap中每个图块的右、上两边会预留指定大小的空白
3. 纹理坐标是[0， 1]之间的浮点数，所以在最终计算覆盖像素区域的时候因取整会引入误差
4. 在lightmap重新pack之后，重新为Renderer计算新的LightmapScaleOffset的时候又会涉及一次整数转浮点数，又一次误差引入


