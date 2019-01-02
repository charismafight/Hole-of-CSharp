# 序列化 

### .net framework官方XmlSerializer类
该类在处理null的字段时会隐藏掉对应节点，以此区分null和""

### DataContractSerializer
如果需要把null值也序列化出来可以使用DataContractSerializer来序列化，但这个类要求xml有序，所以也有局限性

### 显示标记可为空节点[XmlElement(IsNullable = true)]，使用XmlSerializer会自动构造以下xml信息
```
<PathologyID xsi:nil="true" />
```
