# miner-pool-redir
本文面向无经验的朋友。
提前简要说明，若直连效果不好可以选择阿里/腾讯云这种的上海,深圳,广州机房做中转（便宜的足够了，转发这点数据量不怎么要性能），操作上几乎一致，你传我，我传他这种关系。

## 第一步 选购服务器
根据地域不同选购香港、日本、新加坡VPS，例如腾讯云的香港服务器，南方的ping时延就很低，中部可能选择日本会更好。

由于VPS的市场各种各样的都有，便于小白操作的可以选择<a href="https://cloud.tencent.com/product/lighthouse" target="_blank">腾讯轻量应用服务器</a>

记得选择24元一个月，如果没有的话34也可以选择，系统选择ubuntu 20.04 LTS
（当你熟悉后可以自行再去寻找更便宜的主机）

<center><img src="https://github.com/HakozakiSerikaP/miner-pool-redir/blob/main/imgae/tencentcloud.png" alt=""></center>

## 第二步 基本操作
对有SSH需求的朋友，可以先选择【重置密码】，用户名下拉菜单选择【系统默认】ubuntu，密码就是你对登录密码。
使用SSH的教程已经非常多了就不再阐述。

<center><img src="https://github.com/HakozakiSerikaP/miner-pool-redir/blob/main/imgae/tencent_set.png" alt=""></center>

不想折腾可以直接使用【远程登录】下的“一键登录”。

接下来是更新升级ubuntu
```
sudo apt-get update -y
sudo apt-get -y dist-upgrade
sudo apt-get clean
sudo apt-get autoremove
```
分步执行以上命令，期间需要等待一下，是正常的。
## 第三步 
### 安装redir 重定向
```
sudo apt-get install redir -y
```
执行以上命令
安装完成后，设置防火墙（有必要的）
启动ufw
```
sudo ufw enable
```
放行22端口
```
sudo ufw allow 22
```
放行你要在挖矿配置文件中填入的端口号，建议在10000-65000中选取。这里以15555作为例子展示
```
sudo ufw allow 15555
```
输入以下命令检查是否有其它多余的端口被开放，此时建议只开放22和15555
```
sudo ufw status
如果有多余的，执行
sudo ufw deny [多余的端口]
```
## 第四步 启用端口转发
```
sudo redir : [填入第三步输入的端口号] [矿池的域名:端口号]
```
根据本文示例，以供参考。语句意思就是监听本地14444端口，将得到的数据转发到域名(IP)对应开放的端口里
```
sudo redir : 15555 asia1.ethermine.org:14444
```
至此就可以不用黑底白字的界面了。
## 第五步 回到腾讯服务器管理界面
在【防火墙】中添加规则，确认即可。
```
应用类型：自定义
限制来源：不勾
协议：TCP
端口：15555（第三步的）
策略：允许
```
删除默认开启的80，443，22端口（请确保你已经完成上述配置）。
再添加关于22端口的策略为拒绝，当你再需要时再启用它。
此时你的防火墙里应该只有15555和ICMP是允许的。到这里就基本结束了。

<center><img src="https://github.com/HakozakiSerikaP/miner-pool-redir/blob/main/imgae/tencent_fw.png" alt=""></center>

## 第六步 设置挖矿的配置文件
把你原来的矿池域名端口换成你的服务器IP端口
例如原来是
```
asia1.ethermine.org:14444
```
现在换成
```
自己服务器的IP:15555
```
<div align=center>![image](https://github.com/HakozakiSerikaP/miner-pool-redir/blob/main/imgae/miner.png)</div>

具体用的什么内核怎么配置参考即可，开源矿工只需要添加一个矿池配置即可，只需要填入你的服务器信息就行了。
意外地留意到内核抽水连不上矿池就又赚了点w//

虽然自己写的有点啰嗦，但操作过就很简单了。 如果有什么错误可以在issu提出指正。
