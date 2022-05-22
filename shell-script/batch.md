# Batch

```
@echo off
echo ####################
echo hyaline tools
echo run command
echo ####################
echo run in which machine(1/2)
echo 1. 3090
echo 2. 2080
set /p machine_=
echo input your command
set /p com_=

if /i %machine_% == 1 (
echo run on 3090
ssh userName@ip "echo password|%com_%"

if /i %machine_% == 2 (
echo run on 2080Ti
@REM echo "%com_%"
ssh userName@ip "echo password|%com_%"
)

pause
```
