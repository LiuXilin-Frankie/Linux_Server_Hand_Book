# Linux Server usr guid

如果您的浏览器无法完全显示文件，请安装相关的md文件显示插件。

`注意：`请随时备份您的代码，重要代码请同步到github中。服务器不对您的任何文件安全负责！

欢迎使用服务器r730xd，在您使用之前请先阅读服务器使用手册。我将从服务器的架构设计以及您如何快速使用并上手进行介绍。如果您在使用的过程中有任何问题，请及时联系管理员解决，管理员会尽快回复您的需求。

administrator：
+ name: AbsoluteX
+ email: xilinliu@link.cuhk.edu.cn

## usr guid structure
+ server 设计逻辑
  + server 文件树与数据地址
  + frp与端口转发
  + 数据拿取与存储
+ 快速开始
  + ssh远程登录
  + 用户修改密码
  + anaconda 环境配置与使用
  + 配置git

## Server 设计逻辑

### 文件树与数据地址
用户文件存储在 /home/usr_name 中，存储介质为SSD，容量上限为480G。每个用户仅对自己的用户文件具有`可写权限`

数据被存储在 /dev/ 中，存储介质是HDD 18T，主要分为两类：
+ /dev/onedrive-CUHK/: 主要为行情数据，是后台使用 onedrive API同步的行情数据。使用onedrive的原因为最低成本维护数据安全。每个用户仅对数据具有`可读权限`，您可以将数据 cp 到您的用户文件夹（容量有限）或者之后提到的 /dev/tmp/ 中进行进一步操作。

+ /dev/tmp/: 数据暂存路径。每个用户对该文件夹下任何文件有`可读可写权限`。您可以在这里分享您的代码以及数据给其他用户，也可以将您需要进一步操作的大量数据 cp 到这里进行进一步操作。使用前请先在该路径下通过 `mkdir` 创建你的个人文件夹。后期若是该盘频繁出现容量上限，管理员可能会清理该文件，所以请不要直接将您的代码在这里编写。

### frp 原理
我不想将 usr guid 搞成开发手册，但是为了后续的解释不得不在这里介绍frp，具体细节可以参考这篇[博客](https://cloud.tencent.com/developer/article/1631703)。

简单地来说，为了保证用户无论在香港还是北极都能访问，我购买了一个云服务器做转发（就是下图中间的服务器）。用户的ssh请求会先通过`云服务器 -p 6000`再发送到您将要使用的`深度学习服务器`。也就是说，只有您的访问请求是访问 `-p 6000 ipv4address` 是访问`深度学习服务器`的，不带端口号的访问请求一律视为只访问腾讯云服务器。

![frp](/img/frp.png)

### 数据拿取与存储
`注意`：请不要直接在`深度学习服务器`和您的`本地终端`间cp大量数据，这操作不仅导致十分缓慢的数据拿取体验（<300kb/s）。同时还会导致其它用户ssh堵塞。

![frp](/img/frp2.png)

因为所有的ssh请求需要经过`云服务器 -p 6000`转发，导致`云服务器 -p 6000`的带宽限制了文件传输速度。但如果您直接从互联网中下载数据，则并不受限制。如果您要上传大量文件，可能限制仍然非常多。事实上，管理员不建议并且谴责您从服务器向您的本地cp大文件这个行为。

usr从本地cp文件到服务器：
+ 小文件可以直接cp到本地，或者考虑上传github等
+ 大文件可以考虑上传到 google drive，然后使用`wget`命令下载。具体可以参考这篇[博客](https://www.jianshu.com/p/a0173d0bd66e)。

usr从服务器cp文件到本地：
+ 小文件可以直接cp到本地，或者考虑上传github等
+ 大文件十分不建议您这样上传。如果直接cp 100m的文件的话，事实上也只需要花费 300s 也就是 5min。（如果文件超过100m大小。。。你把我服务器当什么了，云盘吗。。。）



## 快速开始

### set up remote ssh
see details on the file ["Setup remote ssh on vscode.pdf"](/Setup%20remote%20ssh%20on%20vscode.pdf)

`注意`：请不要上传公钥到服务器配置免密登录，那将使得整个服务器暴露在外网的攻击下！！！！

服务器仅支持ssh这样的终端登录。不支持图形界面登录（带宽不够）

### 修改密码
输入 “passwd”，修改您的密码，请第一时间修改密码。

![passwd](/img/passwd.png)

### anaconda 环境配置与使用

每个用户的环境自己的home目录.conda/envs下。默认更换了清华大学的镜像源

用户登陆后，应该可以直接使用conda命令。

先初始化：
```shell
conda init bash
```

先看看conda的版本：
```shell
conda --version
```




### 配置git
由于root用户的.gitconfig无法读取，所以需要先创建文件
```
vi .gitconfig
```
退出vim编辑模式

使用 git config --global 指令设置以下信息
+ user.name
+ user.email
+ user.password


保存并退出

查看信息是否生效
```
git config --list
```
print的结果应该是您刚才输入的内容，如果没有输出则表示失败

### pytorch

create the conda env and install library

![install_pytorch](/img/install_pytorch.png)

```shell
conda create -n pytorch_GPU python=3.11
conda activate pytorch_GPU
pip3 install --pre torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/cu121
conda install jupyter
```

我为所有usr准备上手即用的 pytorch gpu 版本，env名就为 pytorch_GPU. 所有usr可用，但不可修改。