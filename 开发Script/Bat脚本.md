---
title: Bat脚本
tags:
  - 脚本
date created: 2023-03-22
date modified: 2024-11-22
---
### OneDrive注册表

```
计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Classes\CLSID\{e88865ea-0e1c-4e20-9aa6-edcd0212c87c}

System.IsPinnedToNameSpaceTree=0
```

### 管理网络驱动器

```
net use Z: \\192.168.2.57\share /user:share HL2024
net use Z: /delete
```

### 浏览器缓存

```
删除 Cache
mklink /D "C:\Users\Raisei\AppData\Local\Microsoft\Edge\User Data\Default\Cache" "V:\EdgeCache"

mklink /D "C:\Users\Raisei\AppData\Local\Google\Chrome\User Data\Default\Cache" "V:\ChromeCache"

mklink /D "C:\Users\Raisei\AppData\Local\Google\Chrome\User Data\Default\Cache" "V:\ChromeCache"

hikari账户
mklink /D "C:\Users\Raisei\AppData\Local\Google\Chrome\User Data\Profile 2\Cache" "V:\ChromeCache2"

```

下载一个叫[**RamDisk**](http://down1.tech.sina.com.cn/download/down_contents/1223740800/41149.shtml)的软件，然后安装，分配256M的内存用来做磁盘，然后修改Chrome快捷方式，在后面加上--User-data-dir=R:\UserData把Chrome的用户目录转到内存盘。效果立马出来，硬盘不再受折磨，速度又回来了。

但是问题又来了，每次重启、关机内存盘都会被清空。
这个可以通过开关机脚本来实现数据同步。
修改开关机脚本
开始→运行，输入gpedit.msc
修改组策略。在 计算机配置→Windows 设置→脚本(启动/关机)

```
　　================

　　开机脚本

　　xcopy /E /Y /K D:\Temp\RamDisk.img\*.* R:\

　　将本地硬盘中存放的用户目录拷贝到内存盘

　　==================

　　关机脚本

　　copy /y "R:\UserData\Local State" D:\Temp\Ramdisk.img\UserData\

　　copy /y R:\UserData\Default\*.* D:\Temp\Ramdisk.img\UserData\Default\

　　xcopy /E /Y /K "R:\UserData\Default\Plugin Data\*.*" "D:\Temp\Ramdisk.img\UserData\Default\Plugin Data"

　　将内存盘用户目录中必要的文件拷贝回本地磁盘指定目录

　　==================
```

# 批量启动软件

```shell
@echo off
start "" "E:\ProgramFileCode\Apifox\Apifox.exe"
start "" "E:\ProgramFileCode\datagrip-2021.2.4.win\bin\datagrip64.exe"
start "" "D:\ProgramFileCode\IntelliJ IDEA 2023.2.1\bin\idea64.exe"
start "" "C:\Program Files\Google\Chrome\Application\chrome.exe" --profile-directory="Profile 2"
start "" "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
start "" "C:\Users\Raisei\AppData\Local\Obsidian\Obsidian.exe"
```

# 清除DNS缓存

```js
ipconfig /flushdns
```

# 单个调试

```shell
@echo off
Setlocal enabledelayedexpansion
::CODER BY Raisei POWERD BY iBAT
::D:\Games\steam\steamapps\workshop\content\431960\
@echo off
setlocal enabledelayedexpansion

set "rootDir=D:\Games\steam\steamapps\workshop\content\431960\1697751839"
set "imgDir=F:\Program Files\wp\wp3\img"

set "outputDir=!rootDir!\output"
set "pkgFile=!rootDir!\scene.pkg"

echo outputDir:!outputDir!
echo pkgFile:!pkgFile!

if exist "!pkgFile!" (
	mkdir "!outputDir!"
	echo Extracting pkgFile...
	repkg.exe extract -o !outputDir! !pkgFile!

	echo Copying jpg files to !imgDir!...

	for %%f in (%outputDir%\materials\*.jpg) do (
		echo Copying %%f...
		copy "%%f" "!imgDir!"
	)
	for %%f in (%outputDir%\materials\*.png) do (
		echo Copying %%f...
		copy "%%f" "!imgDir!"
	)
	echo Files copied successfully.
)
pause
```

`setlocal enabledelayedexpansion` 是批处理脚本中的一个命令，它启用了延迟变量扩展。
在批处理中，变量可以使用 `!` 或 `%` 符号来引用。当启用延迟变量扩展时，变量可以使用 `!` 符号进行扩展，而 `%` 符号则只用于普通的变量扩展。

```shell
set var=hello
echo %var%
echo !var!
```
第一个 `echo` 命令会输出 `%var%`，因为它使用了 `%` 符号进行变量扩展。而第二个 `echo` 命令会输出 `hello`，因为它使用了 `!` 符号进行延迟变量扩展。
启用延迟变量扩展后，可以在批处理中使用 `!` 符号来引用变量，从而避免了一些变量扩展的问题。

解压？
```shell
echo Extracting !zipFile!...
powershell -command "& {Add-Type -AssemblyName System.IO.Compression.FileSystem; [System.IO.Compression.ZipFile]::ExtractToDirectory('!zipFile!', '!outputDir!');}"
```

# 批量解压复制创意工坊

该脚本会遍历 `data` 目录下的所有子目录，检查每个子目录中是否存在 zip 文件。如果存在，则创建 `output` 目录，将 pkg 文件解压到 `output` 目录，复制其中的 jpg 文件到 `img` 目录，然后删除 `output` 目录。

```shell
@echo off
setlocal enabledelayedexpansion
set "rootDir=D:\Games\steam\steamapps\workshop\content\431960"
set "imgDir=F:\Program Files\wp\wp3\img2"

for /D %%d in (!rootDir!\*) do (
    set "outputDir=%%d\output"
    set "pkgFile=%%d\scene.pkg"

	echo outputDir:!outputDir!
	echo pkgFile:!pkgFile!
	
	if exist "!pkgFile!" (
		mkdir "!outputDir!"
		echo Extracting pkgFile...
		repkg.exe extract -o !outputDir! !pkgFile!
	
		echo Copying jpg files to !imgDir!...
	
		for %%f in (!outputDir!\materials\*.jpg) do (
			echo Copying %%f...
			copy "%%f" "!imgDir!"
		)
		for %%f in (!outputDir!\materials\*.png) do (
			echo Copying %%f...
			copy "%%f" "!imgDir!"
		)
		echo Files copied successfully.
	)
)
pause
```

# Bat的变量引用

谜一般的变量引用，在setlocal enabledelayedexpansion的前提下
单个循环里使用\%%正常引用，嵌套循环则需使用！！

# 解析单个PKG文件

移动steam的所有mp4文件
```shell
@echo off
for /r %%a in (*.mp4) do (
echo %%a
copy "%%a" "F:\Program Files\wp\wp3"
)
pause
```
解压pkg并提取图片，先把repkg.exe所在目录加入环境变量，稍微改一下可以变成解压并移动所有图片，但我移动完了懒得搞了
```shell
@eche off
repkg.exe extract -o ./output ./scene.pkg && cd output/materials && for /r %%a in (*.png) do (
copy "%%a" "F:\Program Files\wp\wp3"
) && for /r %%a in (*.jpg) do (
copy "%%a" "F:\Program Files\wp\wp3"
)
pause
```

# Clash端口号为0

网络上大家对于这个问题出现的原因分析，大多是说系统更新后，由于使用了`Hyper-V`的功能，虚拟机需要映射一部分端口，并且在系统更新后对动态映射的端口范围进行了更改，导致占用了本来的`Clash`使用的端口。所以为了解决这个问题，

- 要么直接关闭`Hyper-V`。（怎么可能，`WSL`不用了？）
- 要么修改系统动态映射的端口范围。（我觉得还是不要动系统层面的设置，免得又出问题。）
- 要么对`Clash`使用的代理端口进行修改，改为不被系统占用的端口即可。（Nice！）

使用命令查看被系统排除在可用范围外的端口：

```shell
netsh interface ipv4 show excludedportrange protocol=tcp
```

# 替换图床服务器

接下来，运行下面这段 java 脚本，替换所有md文件的图床服务器前缀
```java
//作者：数码科技宅  
//链接：https://www.zhihu.com/question/524094354/answer/2408710720
import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
​
/**
 * @ClassName ChangePicPath
 * @Description 修改指定文件夹中的所有信息
 * @Author faro_z
 * @Date 2022/3/26 5:18 下午
 * @Version 1.0
 **/
public class ChangePicPath {
    private static String folderName = "<你的文章所在的文件夹的全路径名>";
    private static String from = "<图片访问路径中待替换的部分>";
    private static String to = "<图片访问路径中被替换成的部分>";
    private static List<String> errFileNameList = new ArrayList<>();
​
    public static void main(String[] args) throws IOException {
        List<String> pathList = getAllFileName(folderName);
        System.out.println("成功获取所有文件名称！");
        System.out.println("第一个文件名称为:"+pathList.get(0));
        // 如果发现文件名称和预期的不一样，别误操作了
        System.out.println("输入任意数字，再按回车键继续...");
        new Scanner(System.in).nextInt();
        int errCount = 0;
        for (String path : pathList) {
            if (!changeFileContent(from,to,path)) {
                errCount++;
            }
        }
        System.out.println("出错文件数为："+errCount);
        System.out.println("所有出错文件名为:");
        for (String errPath : errFileNameList) {
            System.out.println(errPath);
        }
    }
​
    /**
     * 获取当前文件夹下所有文件名（不包括文件夹名）
     * @param folderName
     * @return
     */
    private static List<String> getAllFileName(String folderName) {
        ArrayList<String> filePathList = new ArrayList<>();
        dfs(folderName,filePathList);
        return filePathList;
    }
​
    /**
     * 递归获取文件名
     * @param path
     * @param filePathList
     */
    private static void dfs(String path, List<String>filePathList) {
        File file = new File(path);
        if (file.isFile() && file.getName().endsWith(".md")) {
            filePathList.add(file.getAbsolutePath());
            return;
        }
        if (file.isDirectory()) {
            File[] files = file.listFiles();
            for (File tmpFile : files) {
                dfs(tmpFile.getAbsolutePath(),filePathList);
            }
        }
    }
​
    private static boolean changeFileContent(String from,String to,String filePath) {
        File file = new File(filePath);
        try {
            FileReader fileReader = new FileReader(file);
            BufferedReader bufferedReader = new BufferedReader(fileReader);
​
            CharArrayWriter tempStream = new CharArrayWriter();
​
            String line = null;
​
            while ((line = bufferedReader.readLine()) != null) {
                line = line.replaceAll(from,to);
                tempStream.write(line);
                tempStream.append(System.getProperty("line.separator"));
            }
​
            bufferedReader.close();
            // 将内存中的流 写入 文件
            FileWriter out = new FileWriter(file);
            tempStream.writeTo(out);
            out.close();
        } catch (IOException e) {
            errFileNameList.add(filePath);
            return false;
        }
        return true;
    }
}
```

# 隐藏窗口启动

```shell
if "%1"=="hide" goto CmdBegin
start mshta vbscript:createobject("wscript.shell").run("""%~0"" hide",0)(window.close)&&exit
:CmdBegin

java -jar kiftd-1.1.0-RELEASE.jar > log.txt
```

# 批量移动文件

```shell
@echo off
for /r %%a in (正则表达式) do (
echo %%a
move "%%a" "%cd%"
)
pause
```
- /r 递归扫描当前文件夹及其子文件夹
- \%%a 临时变量，当前指向的文件路径
- in（） 条件，对满足此条件的文件执行后边的 do 里的内容
- do 执行移动操作，这里边还用 echo 做了个输出，可有可无
- move source target 移动操作，这里注意，这两个地址中不能包含空格，如果有，要像代码中用引号把这两个变量括起来。
- %cd% 只当前目录，也就是这个bat文件所在的目录

# 复制文件夹

以下是一个可用的示例脚本，可以将指定文件夹下的所有jpg、png和zip文件复制到另一个目录中：

```shell
@echo off
set source=C:\source_folder
set destination=C:\destination_folder

xcopy "%source%\*.jpg" "%destination%" /s /y
xcopy "%source%\*.png" "%destination%" /s /y
xcopy "%source%\*.zip" "%destination%" /s /y

echo Files copied successfully.
pause
```

请注意，您需要将“source_folder”和“destination_folder”替换为实际的文件夹路径。此脚本使用xcopy命令来复制文件，并使用/s选项来包括子文件夹，/y选项来覆盖目标文件夹中的任何现有文件。最后，脚本会输出一条消息，指示文件已成功复制，并等待用户按任意键以关闭窗口。

# xcopy

xcopy和copy命令都是用于复制文件的Windows命令。它们的主要区别在于它们的功能和选项。

copy命令只能复制单个文件，而xcopy命令可以复制整个文件夹及其子文件夹。xcopy命令还可以复制文件的属性和时间戳，而copy命令不支持这些功能。

另外，xcopy命令有更多的选项和参数可用于更精确地控制复制过程，例如/s选项用于包括子文件夹，/d选项用于仅复制源文件夹中更新的文件，/c选项用于继续复制即使发生错误等等。

总之，如果您需要复制整个文件夹或需要更高级的选项和参数，则应使用xcopy命令。如果您只需要复制单个文件，则可以使用copy命令。

# 删除空的文件夹

当上述操作完成后，把空壳文件夹删除的需求可能就产生了。代码如下：
```shell
@echo off
 
for /f "delims=" %%a in ('dir /ad /b /s %cd%^|sort /r') do (
 
rd "%%a">nul 2>nul &&echo 空目录"%%a"成功删除！

)

pause
```

# 开启热点

```shell
netsh wlan set hostednetwork mode=allow ssid=ThinkBook16P key=17679395638

netsh wlan start hostednetwork
```

# 批量启动程序

start开启新窗口，不然只能启动一个程序
/k执行完不关闭，/c执行完关闭窗口
&&连接执行语句
多个语句时用“”，单个语句不要用“”包起来，可能报错

```shell
cd FinalProject\newsapi
start cmd /k "conda activate ocr&&python manage.py runserver 0.0.0.0:8000"

cd ..\..\News-Page
start npm run dev

cd ..\vue-admin-template-master
start  npm run serve
```

据了解，是因为我此次执行的命令是三个没有关联的命令，直接start就行了，网上查的用&，&&，||连接的情况适用于多条有关联的命令一起执行的情况，如下：

```shell
 命令1 & 命令2 & 命令3 ... (无论前面命令是否故障,照样执行后面)   
 命令1 && 命令2 && 命令3....(仅当前面命令成功时,才执行后面)   
 命令1 || 命令2 || 命令3.... (仅当前面命令失败时.才执行后面)
```

下面教大家如何使用bat写一段自动执行cmd启动项目的脚本。

首先，我们新建一个txt的文件，输入下方代码
```shell
cmd /k "cd /d 路径&&命令"
例如E盘下的A文件下的B文件，命令是anywhere
cmd /k "cd /d E:\A\B&&anywhere"
```

```bat

@echo off
start "" "E:\ProgramFileCode\Apifox\Apifox.exe"
start "" "E:\ProgramFileCode\datagrip-2021.2.4.win\bin\datagrip64.exe"
start "" "D:\ProgramFileCode\Microsoft VS Code\Code.exe"
start "" "D:\ProgramFileCode\IntelliJ IDEA 2023.2.1\bin\idea64.exe"
start "" "C:\Program Files\Google\Chrome\Application\chrome.exe" --profile- directory="Profile 2"
start "" "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
start "" "C:\Users\Raisei\AppData\Local\Obsidian\Obsidian.exe"

@echo off
start "Apifox" "E:\ProgramFileCode\Apifox\Apifox.exe"
start "datagrip" "E:\ProgramFileCode\datagrip-2021.2.4.win\bin\datagrip64.exe"
start "Microsoft VS Code" "D:\ProgramFileCode\Microsoft VS Code\Code.exe"
start "IDEA" "D:\ProgramFileCode\IntelliJ IDEA 2023.2.1\bin\idea64.exe"
start "Chrome" "C:\Program Files\Google\Chrome\Application\chrome.exe" --profile-directory="Profile 2"
start "Edge" "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
start "Obsidian" "C:\Users\Raisei\AppData\Local\Obsidian\Obsidian.exe"

Set WshShell = CreateObject("WScript.Shell")

WshShell.Run """E:\ProgramFileCode\Apifox\Apifox.exe""", 0, False
WshShell.Run """E:\ProgramFileCode\datagrip-2021.2.4.win\bin\datagrip64.exe""", 0, False
WshShell.Run """D:\ProgramFileCode\Microsoft VS Code\Code.exe""", 0, False
WshShell.Run """D:\ProgramFileCode\IntelliJ IDEA 2023.2.1\bin\idea64.exe""", 0, False
WshShell.Run """C:\Program Files\Google\Chrome\Application\chrome.exe --profile-directory=Profile 2""", 0, False
WshShell.Run """C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe""", 0, False
WshShell.Run """C:\Users\Raisei\AppData\Local\Obsidian\Obsidian.exe""", 0, False


```

# shell

## 1. 批量复制

以下是一个可用的shell脚本示例，可以将指定文件夹下的所有jpg、png和zip文件复制到另一个目录中：

```shell
#!/bin/bash

source=/path/to/source_folder
destination=/path/to/destination_folder

cp $source/*.jpg $destination
cp $source/*.png $destination
cp $source/*.zip $destination

echo "Files copied successfully."
```

请注意，您需要将“/path/to/source_folder”和“/path/to/destination_folder”替换为实际的文件夹路径。此脚本使用cp命令来复制文件，并使用通配符（*）来匹配所有jpg、png和zip文件。最后，脚本会输出一条消息，指示文件已成功复制。
