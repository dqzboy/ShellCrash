# Redmi-AX6000路由器科学上网详细教程

## ShellClash介绍
- 在Linux环境中使用Shell脚本一键部署和管理Clash服务
- 项目地址：https://github.com/juewuy/ShellClash

## 步骤一、解锁路由器SSH
新版版本的Redmi AX6000固件 无需刷固件
### 1、获取stok码
- 登入路由器后台，然后在搜索地址栏复制stok码
- **注意**：`stok`码再每次重新登入路由都会改变，所以以下的步骤都需要是最新的stok码的情况下进行操作
 
> ![image](https://user-images.githubusercontent.com/42825450/215307084-4640f9de-140e-445e-ad5d-ce091713b67b.png)

### 2、开启TELNET
- **注意**：将下面红色标记的对应的stok改为自己上面获取到的路由器对应的stok码
#### 2.1：开启开发/调试模式
```shell
http://192.168.31.1/cgi-bin/luci/;stok=对应的stok/api/misystem/set_sys_time?timezone=%20%27%20%3B%20zz%3D%24%28dd%20if%3D%2Fdev%2Fzero%20bs%3D1%20count%3D2%202%3E%2Fdev%2Fnull%29%20%3B%20printf%20%27%A5%5A%25c%25c%27%20%24zz%20%24zz%20%7C%20mtd%20write%20-%20crash%20%3B%20
```
> ![image](https://user-images.githubusercontent.com/42825450/215307203-7061dd2a-8b07-43bd-b597-af5529a55fb3.png)

#### 2.2：重启路由器
```shell
http://192.168.31.1/cgi-bin/luci/;stok=对应的stok/api/misystem/set_sys_time?timezone=%20%27%20%3b%20reboot%20%3b%20
```
> ![image](https://user-images.githubusercontent.com/42825450/215307224-36051831-ccb1-4c19-a9f5-19bc6634145b.png)
- 等待路由器重启完成，大概2分钟左右时间

#### 2.3：设置Bdata永久开启telnet
- **注意：** 上面重启了路由器之后重新连接无线之后，访问路由器后台需要重新登入，这个时候我们需要获取最新的stok码
> ![image](https://user-images.githubusercontent.com/42825450/215307265-26799c9a-d7f4-4c03-81ce-854fff882417.png)

```shell
http://192.168.31.1/cgi-bin/luci/;stok=对应的stok/api/misystem/set_sys_time?timezone=%20%27%20%3B%20bdata%20set%20telnet_en%3D1%20%3B%20bdata%20set%20ssh_en%3D1%20%3B%20bdata%20set%20uart_en%3D1%20%3B%20bdata%20commit%20%3B%20
```
> ![image](https://user-images.githubusercontent.com/42825450/215307274-03c6a489-b9a9-42ca-b9a2-c36339f7cd3c.png)

#### 2.4：重启路由器
```shell
http://192.168.31.1/cgi-bin/luci/;stok=对应的stok/api/misystem/set_sys_time?timezone=%20%27%20%3b%20reboot%20%3b%20
```
> ![image](https://user-images.githubusercontent.com/42825450/215307288-1620aaef-d72c-4340-8ad6-9af6ef328ce2.png)


### 3、开启SSH
#### 3.1：连接路由器
- 连接到对应的无线WiFi，然后主机输入192.168.31.1，协议选择TELNET 端口默认23
- **提示：** 我这里使用的xshell工具
> ![image](https://user-images.githubusercontent.com/42825450/215307331-1ca2614c-b7c6-4521-81c5-5a6edd216b44.png)
> ![image](https://user-images.githubusercontent.com/42825450/215307332-40e45a74-558c-40af-83dd-769d457ba8ed.png)

#### 3.2：修改root密码
- 连上telnet后运行下列命令
```shell
echo -e 'admin\nadmin' | passwd root
```
- root密码修改为admin
> ![image](https://user-images.githubusercontent.com/42825450/215307356-f391b655-d2a1-48ef-b3f5-86aa928e947e.png)

#### 3.3：固化SSH
- 固化SSH之后，以后升级路由器固件SSH也会保留不会被恢复
```shell
nvram set ssh_en=1
nvram set telnet_en=1
nvram set uart_en=1
nvram set boot_wait=on
nvram commit
```
#### 3.4：永久开启SSH
- 重启不会关闭
```shell
mkdir /data/auto_ssh && cd /data/auto_ssh
curl -O https://cdn.jsdelivr.net/gh/lemoeo/AX6S@main/auto_ssh.sh
chmod +x auto_ssh.sh

uci set firewall.auto_ssh=include
uci set firewall.auto_ssh.type='script'
uci set firewall.auto_ssh.path='/data/auto_ssh/auto_ssh.sh'
uci set firewall.auto_ssh.enabled='1'
uci commit firewall
```

#### 3.5：修改时区设置
```shell
cd
uci set system.@system[0].timezone='CST-8'
uci set system.@system[0].webtimezone='CST-8'
uci set system.@system[0].timezoneindex='2.84'
uci commit
```

#### 3.6：关闭开发/调试模式
```shell
mtd erase crash
```

#### 3.7：重启路由器
```shell
reboot
```
> ![image](https://user-images.githubusercontent.com/42825450/215307420-4d4d3ec8-b5fa-409c-b7e1-efca3064e32b.png)

### 4、连接SSH
- 路由器重启之后，确保PC连接成功，然后再使用xshell工具并选择ssh协议连接路由器
> ![image](https://user-images.githubusercontent.com/42825450/215307443-c1b5861e-3058-4f7b-8357-7be424842ac6.png)
> ![image](https://user-images.githubusercontent.com/42825450/215307448-ef1266e5-8da0-4487-97ce-42505727a0ee.png)
- **提示：** 这里的root密码就是上面修改的密码 admin
> ![image](https://user-images.githubusercontent.com/42825450/215307457-da8d94e8-0c72-45e5-adef-ab6114d66550.png)
> ![image](https://user-images.githubusercontent.com/42825450/215307462-d4a7e041-708c-43a2-9aa6-03924c0fb0e4.png)

## 步骤二、配置科学上网
- 上面步骤一解锁了路由SSH连接之后，现在我们就需要配置路由器科学上网了；
- **注意：** 需提前准备科学上面的订阅地址或者连接地址







