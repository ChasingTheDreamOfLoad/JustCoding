# C++ Base

## Namespace

name space 具有 全局名字空间与**匿名名字空间**

[命名空间 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4)**(reference)**

CMakeLists.txt 不会每一个自己用的cpp都要添加进去吧

## C++ 计算时间 ( no dependencies)

```cpp
clock_t time1 = clock();
// your code
clock_t time2 = clock();
double t = ((double)(time2 - time1)) / CLOCKS_PER_SEC ;
cout << "CLOCKS_PER_SEC: " << CLOCKS_PER_SEC << endl;
cout << "Process running time: " << t << "s" << endl;
```

to  string&#x20;

```
clock_t time = clock();
std::string time_s = std::to_string(time);
pcl::io::savePCDFileASCII ("./pcd_file/test_"+time_s+"pcd.pcd", cloud);
```

**opencv 自带的 计算时间方法** TickMeter

```cpp
 #include "opencv2/opencv.hpp"
 //
 TickMeter meter;
 meter.start();
 meter.stop();
 cout << "Detector initialized in " << std::fixed << std::setprecision(3)
 << meter.getTimeSec() << " seconds" << endl;
 meter.reset();
 
```

****

## **基于范围的for循环(C++11)**

for 语句允许简单的范围迭代：

```cpp
int my_array[5] = {1, 2, 3, 4, 5};
// 每个数组元素乘于 2
for (int &x : my_array)
{
    x *= 2;
    cout << x << endl;  
}
// auto 类型也是 C++11 新标准中的，用来自动获取变量的类型
for (auto &x : my_array) {
    x *= 2;
    cout << x << endl;  
}
```

## **NOTE : 可以用于注释**

```cpp
// NOTE: topic name is important because the optimizer needs to know which sensor it is
// For instance, the topic name requirement is that a lidar sensor should contain 'lidar' in the name.
// Furthermore, stereo camera should contain 'stereo', monocular camera should contain 'mono' and radar sensor should contain 'radar')
```

## c++中的<>代表C++模板

模板是C++支持参数化多态的工具，使用模板可以使用户为类或者函数声明一种一般模式，使得类中的某些数据成员或者成员函数的参数、返回值取得任意类型。

模板的一般定义为

```cpp
template<typename T>
void moveFromROSMsg(sensor_msgs::PointCloud2 &cloud, pcl::PointCloud<T> &pcl_cloud)
{
  pcl::PCLPointCloud2 pcl_pc2;
  pcl_conversions::moveToPCL(cloud, pcl_pc2);
  pcl::fromPCLPointCloud2(pcl_pc2, pcl_cloud);
}
```

```cpp
template <typename type> ret-type func-name(parameter list)
{
   // 函数的主体
}
```

