
```markdown
# 企业网 ACL 访问控制实验（eNSP 仿真）

## 实验需求
1. **Telnet 管理限制**：只允许 PC1（192.168.10.1）通过 Telnet 登录总部核心路由器 R4
2. **ICMP 访问隔离**：禁止 PC2（192.168.20.1）ping 通 R4

## 核心配置

### 1. 基本 ACL（控制 Telnet 登录）
```

acl 2000
rule 5 permit source 192.168.10.1 0.0.0.255
rule 10 deny source any

```
```

user-interface vty 0 4
acl 2000 inbound

```

### 2. 高级 ACL（禁止 ping）
```

acl 2001
rule 3 deny source 192.168.20.1 0

```
```

interface GigabitEthernet0/0/2
traffic-filter inbound acl 2001

```

## 验证结果
| 测试项 | 预期结果 | 实际结果 | 状态 |
|--------|----------|----------|------|
| PC1 telnet R4 | 成功登录 | | ✅ |
| PC2 telnet R4 | 拒绝访问 | | ✅ |
| PC2 ping R4 | 超时 | | ✅ |

## 使用的软件
华为 eNSP
```