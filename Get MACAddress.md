# 获取MAC地址时出现的问题

1. 通过网络获取SendARP方式，此方法依赖网络，并且依赖安全设置，例如访问腾讯云服务器时获取到的是00000000000000
2. 获取本地MAC时如果本地网卡没接入局域网或公网，同样可能导致获取不到地址
3. 离线获取相对较好的方式见下

```
            foreach (NetworkInterface adapter in nics)
            {
                if (sMacAddress == String.Empty)// only return MAC Address from first card  
                {
                    //IPInterfaceProperties properties = adapter.GetIPProperties(); Line is not required
                    sMacAddress = string.Join("-", (from z in adapter.GetPhysicalAddress().GetAddressBytes() select z.ToString("X2")).ToArray());
                }
            }
```
