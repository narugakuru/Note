### Clash端口号为0
网络上大家对于这个问题出现的原因分析，大多是说系统更新后，由于使用了`Hyper-V`的功能，虚拟机需要映射一部分端口，并且在系统更新后对动态映射的端口范围进行了更改，导致占用了本来的`Clash`使用的端口。所以为了解决这个问题，

-   要么直接关闭`Hyper-V`。（怎么可能，`WSL`不用了？）
-   要么修改系统动态映射的端口范围。（我觉得还是不要动系统层面的设置，免得又出问题。）
-   要么对`Clash`使用的代理端口进行修改，改为不被系统占用的端口即可。（Nice！）

使用命令查看被系统排除在可用范围外的端口：

```shell
netsh interface ipv4 show excludedportrange protocol=tcp
```

### 替换图床服务器
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


### 隐藏窗口启动
```shell
if "%1"=="hide" goto CmdBegin
start mshta vbscript:createobject("wscript.shell").run("""%~0"" hide",0)(window.close)&&exit
:CmdBegin

java -jar kiftd-1.1.0-RELEASE.jar > log.txt
```

### 批量移动文件
```shell
@echo off
for /r %%a in (正则表达式) do (
echo %%a
move "%%a" "%cd%"
)
pause
```
- /r        递归扫描当前文件夹及其子文件夹
- \%%a  临时变量，当前指向的文件路径
- in（） 条件，对满足此条件的文件执行后边的 do 里的内容
- do 执行移动操作，这里边还用 echo 做了个输出，可有可无
- move  source  target  移动操作，这里注意，这两个地址中不能包含空格，如果有，要像代码中用引号把这两个变量括起来。
- %cd%  只当前目录，也就是这个bat文件所在的目录


### 删除空的文件夹

当上述操作完成后，把空壳文件夹删除的需求可能就产生了。代码如下：
```shell
@echo off
 
for /f "delims=" %%a in ('dir /ad /b /s %cd%^|sort /r') do (
 
rd "%%a">nul 2>nul &&echo 空目录"%%a"成功删除！

)

pause
```

开启热点
```shell
netsh wlan set hostednetwork mode=allow ssid=ThinkBook16P key=17679395638

netsh wlan start hostednetwork
```

### 批量启动程序

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