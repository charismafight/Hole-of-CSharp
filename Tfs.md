tfs的iis身份认证设置中不要开asp.net模拟，打开windows身份认证和基本身份认证即可

### 问题 服务器返回的内容类型的文本text/html
第一步，停止IIS,可以使用cmd命令:iisreset /STOP来停止IIS，在Win 7下使用cmd命令时，请以管理员运行。

第二步，清空TFS的缓存，文件夹的目录为:C:\Program Files\Microsoft Team Foundation Server 2010\Application Tier\Web Services\_tfs_data,其中”C:\Program Files\Microsoft Team Foundation Server 2010\”是TFS的安装目录。

第三步，启动IIS,可以使用cmd命令:iisreset /START.
