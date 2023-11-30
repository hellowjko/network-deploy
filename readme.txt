说明：
网络配置脚本，配置服务器bond以及vlan网络
最新版本v3.1
下载地址：https://github.com/hellowjko/network-deploy/releases

目录结构：
files      存放服务器网络信息包括bond、vlan等
playbook   存放playbook文件
scripts    存放playbook调用的shell脚本文件
start.sh   主运行文件
hosts      ansible主机清单文件

1、填写单元格内不能有空格（重要）
2、子网掩码目前只支持网络前缀格式，例如24。暂不支持255.255.255.0格式
3、以下有相关信息的需填写，无地址的留空
4、bond接口MTU必填，一般业务管理(千兆电口)为1500，其他类型(万兆光口)为9000,特殊情况根据实际填写,vlan接口mtu不能大于bond接口mtu值
5、不要修改文件名,必须为server-type.csv、server-bond.csv、server-vlan.csv、bond-method.txt(重要)

############################################
server-type.csv根据IP地址规划表进行填写:
type:    (必填)服务器类型代码，role角色一致可使用一种类型代码，每种类型不可重复，可自定义例如:type1,type2...
role:    (选填)服务器类型，例如宿主机,弹性裸金属服务器...
network-type: (选填,建议填写)网络类型，例如:openstack管理,计算内网....
bond-mode:    (必填)绑定bond使用的模式(仅支持1/2/4/6)
bond-name:    (必填)bond网卡名称,例如bond0,bond1...
bond-mtu:     (必填)bond mtu值(一般电口为1500,光口为9000)
bond-vlan:    (必填)bond接口vlan子接口,格式bond0.150,bond1.300....
vlan-mtu:     (必填)bond接口vlan子接口mtu值,根据实际情况填写
region-ipv4:    (选填)ipv4路由明细格式11.110.0.0/17,多个路由明细使用"-"隔开
region-ipv6:    (选填)ipv6路由明细格式240E:0108:1186:0001::/64,多个路由明细使用"-"隔开
############################################
server-bond.csv根据IP地址规划表进行填写:
此文件只填写bond接口地址

type:       (必填)服务器类型代码，role角色一致可使用一种类型代码，每种类型不可重复，可自定义例如:type1,type2...
ipmi:       带外地址
sn:         每台服务器的sn各不相同（ 避免都为数字显示科学计数，规定格式：sn-序列号）
role:        (选填)服务器类型，例如宿主机,弹性裸金属服务器...
hostname:    主机名
bond-name：   bond接口名称，如果有此接口但没有地址，只需填写接口名称
ipv4/mask/gw：前一列bond接口的IP地址/子网掩码/网关地址（格式：192.168.1.1/24/192.168.1.254）
......

############################################
server-vlan.csv根据IP地址规划表进行填写:
此文件只填写vlan接口地址

type:    (必填)服务器类型代码，role角色一致可使用一种类型代码，每种类型不可重复，可自定义例如:type1,type2...
ipmi:
sn:    每台服务器的sn各不相同（ 避免都为数字显示科学计数，规定格式：sn-序列号）
bond-name：    bond接口名称，如果有此接口但没有地址，只需填写接口名称
ipv4/mask/gw：    前一列bond接口的IP地址/子网掩码/网关地址（格式：192.168.1.1/24/192.168.1.254）
......

############################################
bond-method.txt填写每种类型服务器的bond成员接口,也可自动生成本文档，网卡名之间用/隔开
bond-method.txt文件格式：
type1 bond0:eth0/eth1
type1 bond1:enp0/enp1
type1 bond2:enp2/enp3
type2 bond0:eth2/eth3
type2 bond1:enp0/enp1/enp2/enp3
type2 bond2:enp4/enp5/enp6/enp7

如需自动生成：
修改../hosts文件[type]部分
每种类型服务器选择一个IP地址填写（选择没有缺少和不能识别的网卡的服务器），用户名密码修改完成，运行
sh start.sh 01
执行完会生成../files/bond-method.txt文件
