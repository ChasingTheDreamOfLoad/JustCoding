---
description: exit ;compare num;color;if;
---

# add\_user



```bash
##!/bin/bash
# set color
colors_On_Red='\033[41m' #Red
colors_On_Green='\033[42m'       # Green
colors_Normal='\e[0m'

# 确保不要在 root 下运行
if [ $UID -eq 0 ]
then
    echo "Don't use this script in root privileges!" 1>&2
    exit 1
fi

# 如果没有输入必要参数(用户名)就退出
argNums=$#
if [ $argNums -eq 0 ]
then
    echo "请添加用户名参数"
    echo "使用示例:bash ./adduser.sh newUserName"
    exit 8
fi


echo "create new user $1"
find_ssh_key=$(echo $(ls ./ssh_keys|grep $1.pub))
# 如果没有在ssh_keys的文件夹中找到匹配用户名的公钥，就退出
if [ -z "$find_ssh_key" ]
then
    echo -e "${colors_On_Red} 用户ssh_key 不存在 ${colors_Normal}"
    exit 8
else
    echo -e "${colors_On_Green}发现用户的ssh_key${colors_Normal}"
fi
# 如果当前名称的用户已经创建，就退出
newUserName=$1
find_exists_user=$(echo $(ls /home|grep $newUserName))
if [ ${#find_exists_user} != 0 ]
then
    echo -e "${colors_On_Red} 用户存在 ${colors_Normal}"
    exit 8
fi
# 创建用户
sudo adduser $newUserName -q
echo "用户创建完成"

# 移动公钥到新建用户的.ssh/authorized_keys 中
sudo mkdir /home/${newUserName}/.ssh
sudo touch /home/${newUserName}/.ssh/authorized_keys
sudo cp /home/${USER}/ssh_keys/$newUserName.pub /home/$newUserName/.ssh/authorized_keys
echo "公钥移动完成"
# 更改公钥权限为 root可写，其他只是可读
sudo chown root /home/$newUserName/.ssh/authorized_keys
sudo chgrp root /home/$newUserName/.ssh/authorized_keys
sudo chmod 644 /home/$newUserName/.ssh/authorized_keys
echo "公钥权限更改完成"

echo "写入cuda路径"
echo 'export LDD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"' |sudo tee -a /home/$newUserName/.bashrc
echo 'export PATH="/usr/local/cuda/bin:$PATH"' |sudo tee -a /home/$newUserName/.bashrc
echo 'export CUDA_HOME=/usr/local/cuda' |sudo tee -a /home/$newUserName/.bashrc

echo "是否给sudo权限，default is no [y/n]"
read isSudo
if [[ ${isSudo} = Y || ${isSudo} = y || {$isSudo} = yes || {$isSudo} = YES ]]
then
    sudo adduser $newUserName sudo
fi
echo "结束"


```
