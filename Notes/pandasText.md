## Pandas数据结构(pandas数据分析)

Series：一维数组，与Numpy中的一维array类似。二者与Python基本的数据结构List也很相近。Series如今能保存不同种数据类型，字符串、boolean值、数字等都能保存在Series中。

```python
import numpy as np
import pandas as pd
data = pd.Series([1, 2, 2, 3, np.NaN, 4, 5])
print(data)
0    1.0
1    2.0
2    2.0
3    3.0
4    NaN
5    4.0
6    5.0
dtype: float64
```

Time- Series：以时间为索引的Series。

DataFrame：二维的表格型数据结构。很多功能与R中的data.frame类似。可以将DataFrame理解为Series的容器。以下的内容主要以DataFrame为主。

Panel ：三维的数组，可以理解为DataFrame的容器。

DataFrame的一些操作

```python
import numpy as np
import pandas as pd
# 以20181010为基点向后生产时间点
# DataFrame生成函数，行索引为时间点，列索引为ABCD
dates = pd.date_range('20181010', periods=6)
t_data = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD'))
print(t_data) 
# 输出维度信息
print(t_data.shape) 
# 输出DataFrame数据值
print(t_data.values)
# 输出DataFrame头部数据，默认为前5行
print(t_data.head())
# 输出输出DataFrame第一行数据
print(t_data.head(1))
# 输出DataFrame尾部数据，默认为后5行
print(t_data.tail())
# 输出输出DataFrame最后一行数据
print(t_data.tail(1))
# 输出行索引
print(t_data.index)
# 输出列索引
print(t_data.columns)
# 输出DataFrame详细信息
print(t_data.describe())
'''
                   A         B         C         D
2018-10-10 -1.167943  0.180400 -0.537615 -0.321035
2018-10-11  1.162420 -0.607885 -1.459871  0.196710
2018-10-12 -0.325212 -0.084843 -1.656363 -0.215035
2018-10-13  1.220920  0.497457 -2.127406 -1.989558
2018-10-14 -0.107073  0.500932  2.276144 -0.199548
2018-10-15  1.358765 -0.052655  0.417499  1.012035
(6, 4)
[[-1.16794252  0.18039963 -0.53761455 -0.32103543]
 [ 1.1624196  -0.60788507 -1.45987142  0.19671025]
 [-0.32521203 -0.08484264 -1.65636295 -0.21503478]
 [ 1.22091989  0.49745728 -2.12740611 -1.98955846]
 [-0.10707329  0.50093245  2.27614417 -0.1995485 ]
 [ 1.35876541 -0.05265517  0.41749886  1.01203451]]
                   A         B         C         D
2018-10-10 -1.167943  0.180400 -0.537615 -0.321035
2018-10-11  1.162420 -0.607885 -1.459871  0.196710
2018-10-12 -0.325212 -0.084843 -1.656363 -0.215035
2018-10-13  1.220920  0.497457 -2.127406 -1.989558
2018-10-14 -0.107073  0.500932  2.276144 -0.199548
                   A       B         C         D
2018-10-10 -1.167943  0.1804 -0.537615 -0.321035
                   A         B         C         D
2018-10-11  1.162420 -0.607885 -1.459871  0.196710
2018-10-12 -0.325212 -0.084843 -1.656363 -0.215035
2018-10-13  1.220920  0.497457 -2.127406 -1.989558
2018-10-14 -0.107073  0.500932  2.276144 -0.199548
2018-10-15  1.358765 -0.052655  0.417499  1.012035
                   A         B         C         D
2018-10-15  1.358765 -0.052655  0.417499  1.012035
DatetimeIndex(['2018-10-10', '2018-10-11', '2018-10-12', '2018-10-13',
               '2018-10-14', '2018-10-15'],
              dtype='datetime64[ns]', freq='D')
Index(['A', 'B', 'C', 'D'], dtype='object')
              A         B         C         D
count  6.000000  6.000000  6.000000  6.000000
mean   0.356980  0.072234 -0.514602 -0.252739
std    1.039694  0.419316  1.640481  0.982559
min   -1.167943 -0.607885 -2.127406 -1.989558
25%   -0.270677 -0.076796 -1.607240 -0.294535
50%    0.527673  0.063872 -0.998743 -0.207292
75%    1.206295  0.418193  0.178721  0.097646
max    1.358765  0.500932  2.276144  1.012035'''
```

```python
import numpy as np
import pandas as pd
# 设计一个字典
d_data = {'A':1,'B':pd.Timestamp('20170220'),'C':range(4),'D':np.arange(4)}
print(d_data)
# {'A': 1, 'B': Timestamp('2017-02-20 00:00:00'), 'C': range(0, 4), 'D': array([0, 1, 2, 3])}
# 使用字典生成一个DataFrame
df_data = pd.DataFrame(d_data)
print(df_data)
'''
   A          B  C  D
0  1 2017-02-20  0  0
1  1 2017-02-20  1  1
2  1 2017-02-20  2  2
3  1 2017-02-20  3  3
'''
# DataFrame中每一列的类型
print df_data.dtypes
'''
A             int64
B    datetime64[ns]
C             int64
D             int64
dtype: object
'''
# 打印A列
print(df_data.A)
'''
0    1
1    1
2    1
3    1
Name: A, dtype: int64
'''
# 打印B列
print(df_data.B)
'''
0   2017-02-20
1   2017-02-20
2   2017-02-20
3   2017-02-20
Name: B, dtype: datetime64[ns]
'''
# B列的类型
print type(df_data.B)
# <class 'pandas.core.series.Series'>
```

```python
import numpy as np
import pandas as pd

dates = pd.date_range('20170220',periods=6)
data = pd.DataFrame(np.random.randn(6,4),index=dates,columns=list('ABCD'))
print(data)
#转置
print(data.T)
# 输出维度信息
print(data.shape)
# 转置后的维度信息
print(data.T.shape)
# 将列索引排序
print(data.sort_index(axis = 1))
# 将列索引排序，降序排列, ascending 参数用于控制升序或降序，默认为升序。
print(data.sort_index(axis = 1,ascending=False))
# 将行索引排序，降序排列
print(data.sort_index(axis = 0,ascending=False))
# 按照A列的值进行升序排列
print(data.sort_values(by='A'))
# 输出A列
print(data.A)
# 输出A列
print(data['A'])
# 输出3,4行
print(data[2:4])
# 输出3，4行
print(data['20181012':'20181013'])
# 输出3,4行
print(data.loc['20181012':'20181013'])
# 输出3,4行
print(data.iloc[2:4])
# 输出B,C两列
print(data.loc[['B','C']])
'''
                   A         B         C         D
2018-10-10  0.178896  1.555952 -0.076197  1.502644
2018-10-11 -0.010699 -0.002835 -1.593182  0.447791
2018-10-12 -0.273108  0.573033  1.278976  1.199961
2018-10-13 -1.742092  0.534452 -1.123985  0.221916
2018-10-14 -0.334024 -1.060581  0.719985  1.284927
2018-10-15 -0.626835 -0.930627  0.536470 -1.098884
   2018-10-10  2018-10-11     ...      2018-10-14  2018-10-15
A    0.178896   -0.010699     ...       -0.334024   -0.626835
B    1.555952   -0.002835     ...       -1.060581   -0.930627
C   -0.076197   -1.593182     ...        0.719985    0.536470
D    1.502644    0.447791     ...        1.284927   -1.098884

[4 rows x 6 columns]
(6, 4)
(4, 6)
                   A         B         C         D
2018-10-10  0.178896  1.555952 -0.076197  1.502644
2018-10-11 -0.010699 -0.002835 -1.593182  0.447791
2018-10-12 -0.273108  0.573033  1.278976  1.199961
2018-10-13 -1.742092  0.534452 -1.123985  0.221916
2018-10-14 -0.334024 -1.060581  0.719985  1.284927
2018-10-15 -0.626835 -0.930627  0.536470 -1.098884
                   D         C         B         A
2018-10-10  1.502644 -0.076197  1.555952  0.178896
2018-10-11  0.447791 -1.593182 -0.002835 -0.010699
2018-10-12  1.199961  1.278976  0.573033 -0.273108
2018-10-13  0.221916 -1.123985  0.534452 -1.742092
2018-10-14  1.284927  0.719985 -1.060581 -0.334024
2018-10-15 -1.098884  0.536470 -0.930627 -0.626835
                   A         B         C         D
2018-10-15 -0.626835 -0.930627  0.536470 -1.098884
2018-10-14 -0.334024 -1.060581  0.719985  1.284927
2018-10-13 -1.742092  0.534452 -1.123985  0.221916
2018-10-12 -0.273108  0.573033  1.278976  1.199961
2018-10-11 -0.010699 -0.002835 -1.593182  0.447791
2018-10-10  0.178896  1.555952 -0.076197  1.502644
                   A         B         C         D
2018-10-13 -1.742092  0.534452 -1.123985  0.221916
2018-10-15 -0.626835 -0.930627  0.536470 -1.098884
2018-10-14 -0.334024 -1.060581  0.719985  1.284927
2018-10-12 -0.273108  0.573033  1.278976  1.199961
2018-10-11 -0.010699 -0.002835 -1.593182  0.447791
2018-10-10  0.178896  1.555952 -0.076197  1.502644
'''

```

![](/home/xjm/Desktop/projects/Typora/20170727234536310.png)** axis=1列  axis=0行

```python
import pandas as pd
import numpy as np

dates = pd.date_range('20181010', periods=6)
data = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD'))
print(data)
print(data.mean(axis=1)) # 计算行平均值 axis=0计算列平均值
'''
                   A         B         C         D
2018-10-10 -0.723084  0.038699 -1.423138  1.490902
2018-10-11 -0.207525 -0.023384  1.826338 -0.230119
2018-10-12  0.047822  0.623372  0.091563 -0.526565
2018-10-13 -2.411373  0.282520  0.057402  0.922938
2018-10-14 -1.565927 -0.121117 -1.112107  0.169089
2018-10-15  0.955668  1.843341 -1.209268  0.284555
2018-10-10   -0.154155
2018-10-11    0.341327
2018-10-12    0.059048
2018-10-13   -0.287128
2018-10-14   -0.657515
2018-10-15    0.468574
Freq: D, dtype: float64
'''
```
