
# 查看PyTorch API的指导

#### dir
查找模块中的所有函数和类 为了知道模块中可以调⽤哪些函数和类，我们调⽤ dir 函数。
例如，我们可以查询随机数⽣成模块中的所有 属性：
```python
import torch 
print(dir(torch.distributions)
```
通常，我们可以忽略以 __ 开始和结束的函数（Python 中的特殊对象）或以单个 _ 开始的函数（通常是内部 函数）。根据剩余的函数名或属性名，我们可能会猜测这个模块提供了各种⽣成随机数的⽅法，包括从均匀分 布（uniform）、正态分布（normal）和多项分布（multinomial）中采样。

#### help
查找特定函数和类的⽤法 有关如何使⽤给定函数或类的更具体说明，我们可以调⽤ help 函数。
例如，我们来查看张量 ones 函数的 ⽤法。 
	help(torch.ones) 


# Matplotlib

## plot
matplotlib 库的 pyplot 模块中的 **plot（） 函数**用于制作点 x， y 的二维六边形分箱图。

> **语法：** matplotlib.pyplot.plot（\*args， scalex=True， scaley=True， data=None， \*\*kwargs）
> 
> **参数：**此方法接受下面描述的以下参数：
> 
> -   **x， y：**这些参数是数据点的水平和垂直坐标。x 值是可选的。
> -   **FMT：**此参数是可选参数，它包含字符串值。
> -   **数据：**此参数是可选参数，它是具有标记数据的对象。
> 
> **返回：** 这将返回以下内容：
> 
> -   **线：**这将返回表示绘制数据的 Line2D 对象列表。


#### gca（）
函数用于获取当前图形上与给定关键字 args 匹配的当前 Axes 实例，或者创建一个

#### pyplot.grid（） 

matplotlib 库的 pyplot 模块中的 **grid（） 函数**用于配置网格线。

> **语法：** matplotlib.pyplot.grid（b=None， which='major'， axis='both'， \*\*kwargs）
> 
> **参数：**此方法接受以下参数。
> 
> -   **乙 ：**此参数是可选参数，无论是否显示网格线。
> -   **哪：**此参数也是一个可选参数，它是要应用更改的网格线。
> -   **轴：**此参数也是一个可选参数，它是应用更改的轴。
> 
> **返回：**此方法不返回任何值。

#### hasattr(obj,key)
函数是一个内置的实用程序函数，用于检查对象是否具有给定的命名属性，如果存在，则返回 true，否则返回 false。

#### plt.legend( )创建图例
> 比plt.show()功能多一点

plt.legend( )_中有_handles_、_labels_和_loc_三个参数，其中：

>**handles**需要传入你所画线条的**实例对象**，这个我也解释不清楚......
  **labels**是图例的名称（能够覆盖在plt.plot( )中label参数值）
  **loc**代表了图例在整个坐标轴平面中的位置（一般选取'best'这个参数值）

## python基础api

#### isinstance(object, classinfo)
会认为子类是一种父类类型，考虑继承关系。

#### zip（\*args）
方法采用可迭代或容器并返回单个迭代器对象，该对象具有来自所有容器的映射值。它用于映射多个容器的类似索引，以便仅使用单个实体即可使用它们。

# Pytorch

#### Tensor的属性
- data: 即存储的数据信息。
- requires_grad: 设置为True，则表示该Tensor需要求导。
- grad: 该Tensor的梯度值， 每次在计算backward时都需要将前一时刻的梯度归零，否则梯度值会一直累加。
- grad_fn: 用于指示梯度函数是哪种类型，叶子节点通常为None，只有结果节点的grad_fn才有效。
- is_leaf: 用来指示该Tensor是否是叶子节点。