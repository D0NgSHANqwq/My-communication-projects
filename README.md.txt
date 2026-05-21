```markdown
# 企业网 DHCP+NAT 综合实验（eNSP 仿真）

## 实验需求
1. **DHCP动态分配**：PC1/PC2通过接口地址池获取IP，PC3通过全局地址池获取IP
2. **静态路由互通**：配置静态路由，实现内网→出口路由器→外网的连通
3. **NAT公网访问**：私网主机访问外网时，源地址转换为公网IP 1.1.1.3

## 核心配置

### 1. DHCP配置

**接口地址池（Vlanif10）**
```

dhcp enable
interface Vlanif10
dhcp select interface
dhcp server dns-list 114.114.114.114


```

**全局地址池（Vlanif40）**
```

ip pool vlanif40
gateway-list 192.168.4.254
network 192.168.4.0 mask 255.255.255.0
interface Vlanif40
dhcp select global

```

### 2. 静态路由配置

**SW2**
```

ip route-static 1.0.0.0 255.0.0.0 192.168.3.2

```

**AR1（出口路由器）**
```

ip route-static 192.168.1.0 24 192.168.3.1
ip route-static 192.168.2.0 24 192.168.3.1
ip route-static 192.168.4.0 24 192.168.3.1

```

### 3. NAT配置
```

nat address-group 1 1.1.1.3 1.1.1.3
acl 2000
rule permit source 192.168.1.0 0.0.0.255
rule permit source 192.168.2.0 0.0.0.255
rule permit source 192.168.4.0 0.0.0.255
interface GigabitEthernet0/0/1
nat outbound 2000 address-group 1

```

## 验证结果
| 验证项 | 预期结果 | 实际结果 | 状态 |
|--------|----------|----------|------|
| PC1获取IP | 192.168.1.x | | ✅ |
| PC2获取IP | 192.168.2.x | | ✅ |
| PC3获取IP | 192.168.4.x | | ✅ |
| PC访问外网 | 源地址转为1.1.1.3 | | ✅ |

## 使用的软件
华为 eNSP