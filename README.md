小米万兆路由器开启ssh
============
你需要的：一部小米万兆路由器，一个USB存储设备（例如U盘，移动硬盘），一个脑子  
  
首先安装docker，具体过程不再赘述。打开小米万兆路由器的simple docker，登录后左边选择“存储管理”，随便选择一个卷点详情，右侧会弹出这样的窗口  
  
选择红框这一部分路径（/mnt/usb-xxxxxxxx/）复制，接着左边“镜像管理”然后随便选择一个镜像点启动镜像（绿色的箭头）然后选择“专业模式”，直接下一步，存储信息界面选择新增挂载，宿主机目录粘贴你刚才复制的路径并加上“../../..”，让路径看起来像这样子：“/mnt/usb-xxxxxxxx/mi_docker/../../..”，容器目录填“/test”（test可以替换为其它，后面的记得改过来）然后点OK，下一步。网络信  息和环境信息界面直接下一步，接着点确定创建，稍等一会儿就会显示创建成功。  
接下来选择操作一栏下左开始数第二个按钮“终端命令”，进入到控制台后输入以下命令并回车：  
cd /test  
vi ./etc/init.d/dropbear  
接着定位到如图所示的位置并删除红框处的代码（start_service函数前六行）（请自行搜索使用vi编辑器的方法）  
  
保存后退出，输入以下代码并回车：  
chroot ./  
/etc/init.d/dropbear start  
如果没有出现错误那么ssh服务应该已经开启了。  
这时候还需要修改密码：输入passwd root并回车，输入两次新密码就好了。  
  
然后就可以连接了。  
  
接下来还可以固化ssh使重启后仍可连接。  
依次输入以下代码并回车：  
nvram set ssh_en=1  
nvram set telnet_en=1  
nvram set uart_en=1  
nvram set boot_wait=on  
nvram commit  
mkdir /data/auto_ssh && cd /data/auto_ssh  
curl -O https://cdn.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh  
chmod +x auto_ssh.sh  
  
uci set firewall.auto_ssh=include  
uci set firewall.auto_ssh.type=&apos;script&apos;  
uci set firewall.auto_ssh.path=&apos;/data/auto_ssh/auto_ssh.sh&apos;  
uci set firewall.auto_ssh.enabled=&apos;1&apos;  
uci commit firewall  
（代码来源于群内巨巨）  
未经许可，严禁转载