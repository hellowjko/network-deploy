说明：
网络配置脚本，配置服务器bond以及vlan网络

修改server-network/files/目录下3个文件
1. server-ipinfo.xlxs
2. server-type.xlxs
以上两个文件需必须另存为utf-8 csv格式文件（重要）

3. bond-method.txt
bond成员接口文件，手工填写或者使用collect-network.sh脚本自动生成

运行：
1.配置文件填写完，上传到network-deploy/files目录
2.进入server-network目录下运行
3.配置服务器网络
ansible-playbook network.yml



