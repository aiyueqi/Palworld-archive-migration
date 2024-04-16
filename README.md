# Palworld-archive-migration
幻兽帕鲁联机存档迁移-联机主机更换

## 背景
最开始通过邀请码模式和朋友开黑，有C1和C2两台电脑，其中C1是主机。后来发现每玩半个小时左右，因为主机C1的内存空间不足，游戏就会卡死。后发现C2电脑的内存更大，且满足官方推荐的配置（32GB+），于是想将主机由C1迁移到C2。

## 基本概念
联机主要分为两种模式，第一种是通过邀请码加入，第二种是通过专用服务器加入，下面分别介绍。

### 邀请码模式
最初使用邀请码模式进行联机，此模式下一定需要主机C1的角色先进入游戏，然后电脑C2才能通过主机C1的邀请码进入游戏，且每次启动邀请码都会变化。
C1和C2的存档位置默认均在以下路径：
```
C:\Users\用户名\AppData\Local\Pal\Saved\SaveGames\你的SteamId\序列码
```
观察可发现，主机C1和电脑C2的存档文件会有些区别，且大部分存档文件仅存在主机H上（如角色信息，世界信息等）
主机C1：
<img width="221" alt="H" src="https://github.com/aiyueqi/Palworld-archive-migration/assets/6499112/69bbfd68-4be9-4b27-bac9-4caa1bececa7">
电脑C2：
<img width="227" alt="C" src="https://github.com/aiyueqi/Palworld-archive-migration/assets/6499112/8746326a-27fb-48a3-8ee8-4eaa26a515ec">

为方便描述，我们称C1电脑下的角色为P1，C2电脑下的角色为P2。后面多次尝试将C1存档路径下的文件移动到C2对应路径下，并进行修改。但会发现移动之后C2下的角色变成了P1。因为直接移动存档文件无法达到目的，且sav格式的文件非文本格式，不容易手动修改，后面查阅文档和攻略后，发现大部分都是针对专用服务器的方案，于是决定改用将邀请码模式转为专用服务器模式。

### 专用服务器模式
此模式下首先运行一个专用服务器（这个服务器可以在C1，C2或是其他的主机上），然后C1、C2在游戏客户端中，可通过专用服务器加入。此时C1和C2的地位是平等的，可各自单独加入，也可同时加入。
steam下PalServer的打开方式：
首先要在游戏库中，勾选“工具”：
<img width="159" alt="s1" src="https://github.com/aiyueqi/Palworld-archive-migration/assets/6499112/27e66609-6640-4da7-8763-5ae1b08f81b2">
然后会发现出现了Palworld Dedicated Server的图标，配置好后点击运行即可启动游戏专用服务器
<img width="203" alt="s2" src="https://github.com/aiyueqi/Palworld-archive-migration/assets/6499112/3174be7a-923c-42ba-af9f-06f8928ffe82">
启动时选择"Open and start as a community server"
<img width="357" alt="s3" src="https://github.com/aiyueqi/Palworld-archive-migration/assets/6499112/670300c4-6fad-47a9-814c-6b910816fa49">


专用服务器的存档位置默认在以下路径：
```
C:\Program Files (x86)\Steam\steamapps\common\PalServer\Pal\Saved\SaveGames\0\
```
## 迁移过程
1. 将C1机器本地存档文件夹下，C:\Users\用户名\AppData\Local\Pal\Saved\SaveGames\你的SteamId\序列码xxx 目录下，除了WorldOption.sav以外的所有文件，copy到C2机器专用服务器文件夹下 C:\Program Files (x86)\Steam\steamapps\common\PalServer\Pal\Saved\SaveGames\0\序列码xxx
2. 此时C2存档迁移已完成，打开C2上的专用服务器和帕鲁游戏程序，验证人物等级，物品，帕鲁等与存档一致
3. C1存档的迁移会比较复杂，进入C1机器的C:\Users\用户名\AppData\Local\Pal\Saved\SaveGames\你的SteamId\序列码xxx\Players目录，可发现有两个文件：一个是00000000000000000000000000000001.sav，另一个是假设ABABABABABABABABABABABABABAB1234.sav，前者是原来的主机C1的角色P1对应的序列号，后者是P2的序列号。实测发现，C2成为新的专用服务器之后，序列号未发生改变，还是ABABABABABABABABABABABABABAB1234.sav。而C1加入新的专用服务器后，P1对应的序列号会发生改变，导致存档丢失，此时需要使用github上的一些开源脚本去修改P1对应的序列号
4. 启动C1的帕鲁程序，输入C2的ip和port，其中帕鲁服务器的port默认是8211，加入专用服务器，并创建新角色，进入世界。
6. 查看C2的C:\Program Files (x86)\Steam\steamapps\common\PalServer\Pal\Saved\SaveGames\0\序列码xxx\Players目录，发现有新的文件生成，即为P1对应的新的序列号，称为P1'，假设是CDCDCDCDCDCDCDCDCDCDCDCDCDCD5678.sav
7. 先做好备份，然后使用https://github.com/xNul/palworld-host-save-fix 工具，执行
```
python fix_host_save.py C:\Program Files (x86)\Steam\steamapps\common\PalServer\Pal\Saved\SaveGames\0\序列码xxx CDCDCDCDCDCDCDCDCDCDCDCDCDCD5678 00000000000000000000000000000001 False
```
8. C1存档迁移完成

## 常见问题
1. 因为palworld-host-save-fix工具依赖palworld-save-tools，所以使用前需要安装palworld-save-tools，建议安装最新版，序列号转换的成功率会更高。即：
```
python -m pip install palworld-save-tools==0.22.0
%这个是当前最新版，后面可能还会有更新，建议去仓库查看最新tag
%https://github.com/cheahjs/palworld-save-tools/tags
```

## 帕鲁世界配置
文件：C:\Program Files (x86)\steamapps\common\PalServer\Pal\Saved\Config\WindowsServer\PalWorldSettings.ini
```
[/Script/Pal.PalGameWorldSettings]
OptionSettings=(PalEggDefaultHatchingTime=0.000000,DeathPenalty=None,BaseCampWorkerMaxNum=20)
```
其他参数修改参考：https://tech.palworldgame.com/settings-and-operation/configuration/

## 参考资料
https://tech.palworldgame.com/
https://github.com/xNul/palworld-host-save-fix
https://github.com/cheahjs/palworld-save-tools
