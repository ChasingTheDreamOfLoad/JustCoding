# GPU smi

Some command is correct in the standard shell. But in this file, it doesn't work. So I give up. It is incomplete.

```bash
#!/bin/bash
# GPU_view.sh
colors_On_Red='\033[41m' 
colors_Green='\033[0;32m'        # Green
colors_On_Green='\033[42m'       # Green
colors_Normal='\e[0m' 
# GPU_NUM represents the number of GPUs in your computer.
GPU_NUM=8

nvidia-smi|grep %|awk '{print $4}' > GPU_test_state_1999
nvidia-smi > GPU_smi_1999

power=$(nvidia-smi|grep %|awk '{print $5}');
# power_without_w=${power//W/}
# echo ${power//W/}
echo "$colors_Green 当前服务器 GPU 使用情況 （仅供参考）$colors_Normal"
for i in $(seq 1 ${GPU_NUM})
do
this_gpu_status=$(cat GPU_test_state_1999|awk "NR==${i}{print;exit}");
user_id=$(cat GPU_smi_1999|grep "N/A  N/A"|grep -v "/usr/lib/xorg/Xorg"|grep " `expr $i - 1` "|awk '{print $5}');
# gpu_power=$(echo ${power//W/}|awk "NR==${i}{print;exit}");
# echo $gpu_power
if [ -n "$user_id" ]
then
gpu_user=$(ps -o user:20 --no-header -p ${user_id})
else
gpu_user="no user"
fi
if [ $this_gpu_status = P8 ]
then
echo "$colors_On_Green GPU`expr $i - 1` 可以使用,${gpu_user}  在使用 $colors_Normal"
else 
echo "$colors_On_Red GPU`expr $i - 1` 完全占用,${gpu_user}  在使用 $colors_Normal"
fi


done

# remove temp file
rm ./GPU_smi_1999
rm ./GPU_test_state_1999 


# test
# GPU_num=`nvidia-smi|grep -c %`
# GPU_status=$(nvidia-smi|grep %|awk '{print $4}')
# for i in $(seq 1 10)
# do   
# # echo ${GPU_status}
# this_gpu_status=$(nvidia-smi|grep %|awk '{print $4}'|awk "NR==${i}{print;exit}")
# # echo ${GPU_status}|awk "NR==${i}{print;exit}" 
# echo GPU $i is $this_gpu_status
# don
```

```bash
#!/bin/bash
# GPU_view.sh
colors_On_Red='\033[41m' 
colors_Green='\033[0;32m'        # Green
colors_On_Green='\033[42m'       # Green
colors_Normal='\e[0m' 
# GPU_NUM represents the number of GPUs in your computer.
GPU_NUM=8

nvidia-smi > GPU_smi_1999


# cat GPU_smi_1999|grep %|awk '{print $4}' > GPU_test_state_1999

gpu_status=$(cat GPU_smi_1999|grep %|awk '{print $4}')
power=$(cat GPU_smi_1999|grep %|awk '{print $5}');
gpu_power=$(echo ${power//W/});
gpu_mem=$(cat GPU_smi_1999|grep "MiB /"|awk '{print $9}'|sed 's:MiB.*$::g');

echo -e "$colors_Green 当前服务器 GPU 使用情況 （仅供参考）$colors_Normal"
for i in $(seq 1 ${GPU_NUM})
do

this_gpu_status=$(echo ${gpu_status}|awk -va=$i '{print $a}');
#this_gpu_status=$(echo ${gpu_status}|awk -va=$i "NR==${a}{print;exit}");
user_id=$(cat GPU_smi_1999|grep "N/A  N/A"|grep -v "/usr/lib/xorg/Xorg"|grep " `expr $i - 1` "|awk '{print $5}');
this_gpu_power=$(echo ${gpu_power}|awk -va=$i '{print $a}')
this_gpu_mem=$(echo ${gpu_mem}|awk -va=$i '{print $a}')
if [ -n "$user_id" ]
then
gpu_user=$(ps -o user:20 --no-header -p ${user_id}|sed ':a;N;$!ba;s/\n/ /g')
else
gpu_user="no user"
fi
if [[ $this_gpu_status = P8 && $this_gpu_power -lt 40 && $this_gpu_mem -lt 1000 ]]
then
echo -e "$colors_On_Green GPU`expr $i - 1` 可以使用,${gpu_user}  在使用 $colors_Normal"
else 
echo -e "$colors_On_Red GPU`expr $i - 1` 已经占用,${gpu_user}  在使用 status ${this_gpu_status} POWER ${this_gpu_power} men ${this_gpu_mem} $colors_Normal"
fi


done

# remove temp file
rm ./GPU_sm
```
