# ROS Base

advertiseService 提供了 简易的编写 service的方式

完整安装 ROS 后 已经自动安装了 pcl-ros 库

~~<mark style="color:red;">老版本的 ROS 的src 下的 cmakelists 在新版本中可能会有大问题</mark>~~

只是因为我用错了package 的创建工具

可能有两种 生成catkin package的方式

catkin\_create\_pkg 是正确的



cv_bridge -> opencv_

```cpp
// input 'in' type : sensor_msgs::ImageConstPtr const & in
cv_bridge::CvImagePtr cv_image = cv_bridge::toCvCopy(in, "bgr8");
cv::Mat image = cv_image->image;
```

save image

```cpp
#include <opencv2/imgcodecs.hpp>
//
std::vector<int> compression_params;
compression_params.push_back(cv::IMWRITE_PNG_COMPRESSION);
compression_params.push_back(0);//默认是0 ，代表IMWRITE_PNG_COMPRESSION的value，0~9，越大压缩程度越高越耗时
result = cv::imwrite("alpha.png", image, compression_params);
```

{% embed url="https://docs.opencv.org/3.4/d4/da8/group__imgcodecs.html" %}
refenrence
{% endembed %}
