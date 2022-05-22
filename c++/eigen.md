---
description: eigen 矩阵库的使用
---

# Eigen

## install (Ubuntu)

```bash
sudo apt install libeigen3-dev
```

Base Use

使用 pkg-config --cflags eigen3 获取include

Code

```cpp
// test ifstream
#include <iostream>
#include <fstream> //for ifstream
#include <Eigen/Dense>
using namespace std;
int main(int argc, char** argv )
{
    int num_points;
    ifstream infile;
    infile.open("/home/hyaline/small-code-base/C++_code_base/ifstream_test_varible.txt");
    cout << "Reading from the file" << endl; 
    infile >> num_points;
    cout << num_points << endl;
    // float f_data;
    // infile >> f_data;
    // cout << f_data << endl;
    // int num_points = 0;
    Eigen::MatrixXd lidar(3, num_points), camera(3, num_points);

    // infile >> num_points;
    // std::cout << "Num points is:" << num_points << std::endl;

    for (int i = 0; i < num_points; i++) {
        infile >> lidar(0, i) >> lidar(1, i) >> lidar(2, i);
    }
    cout << lidar << endl;
    for (int i = 0; i < num_points; i++) {
        infile >> camera(0, i) >> camera(1, i) >> camera(2, i);
    }
    cout << camera << endl;
    // 注意：不输出肯定不会有输出
    infile.close();
}
```



