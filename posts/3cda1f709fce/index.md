# Ubuntu配置为路由器


<!--more-->

需求：将树莓派配置成中继路由器；树莓派的wlan0连接wifi做wan口，eth0网口连接电脑做lan口，使电脑可以通过网线连接树莓派上网，并支持ipv6。   
## 配置网口
wlan0做wan口，连接wifi，设置为dhcp    
eth0做lan口连接电脑，设置为静态ip   
配置如下：
`vim /etc/netplan/50-cloud-init.yaml`
``` yaml
network:
    ethernets:
        eth0:
            dhcp4: false
            addresses:
            - 192.168.2.1/24
    wifis:
        wlan0:
            dhcp4: true
            nameservers:
                addresses: [8.8.8.8,114.114.114.114,8.8.4.4]
            access-points:
                "HUAWEI-1ER0EH_5G":
                    password: "xxxxxxxxxxx"
    version: 2
    renderer: networkd
```
`netplan apply`   

## 启用IP转发
编辑文件 `vim /etc/sysctl.conf`
修改此行 
``` 
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding = 1
```
应用修改`sysctl -p`

## 配置NAT
使用iptables配置NAT，允许局域网设备通过Ubuntu主机访问互联网:    
`iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE`  
`ip6tables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE`   
安装iptables-perisistent保存iptables配置:    
`iptables-save > /etc/iptables/rules.v4`


## 配置DHCP服务器
- 安装isc-dhcp-server
- 配置isc-dhcp-server
  - vim /etc/default/isc-dhcp-server
    ```
    INTERFACESv4="eth1"
    INTERFACESv6="eth0"
    ```

  - vim /etc/dhcp/dhcpd.conf
    ``` 
    subnet 192.168.2.0 netmask 255.255.255.0 {
        range 192.168.2.10 192.168.2.200;
        option routers 192.168.2.1;
        option broadcast-address 192.168.2.255;
        option domain-name-servers 8.8.8.8, 8.8.4.4;
        default-lease-time 600;
        max-lease-time 7200;
      }
    ```
  - 重启isc-dhcp-server服务
    ``` bash
    systemctl restart isc-dhcp-server
    systemctl enable isc-dhcp-server
    ```

## 使用radvd配置dhcpv6
`apt install radvd`   
`vim /etc/radvd.conf`   
```
interface eth0 {
        AdvSendAdvert on;
        MinRtrAdvInterval 3;
        MaxRtrAdvInterval 10;
        prefix 2001:da8:d805:a210:a1::/64{
        };
};
```
`systemctl restart radvd`  
`systemctl enable radvd`   

---

> 作者: Yu  
> URL: https://blog.zy9826.site/posts/3cda1f709fce/  

