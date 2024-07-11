---
title:  ServeUseGuide
date: 2024-05-25 12:00:00 +/-TTTT
categories: [Serve,Guide]
tags: [Serve，ssh ]  # TAG names should always be lowercase
---

**以下内容为使用服务器时一些配置 不定期更新**

## SSH
### 服务器配置多个ssh密钥 可以通过config传不同仓库
* 修改config
 ```
Host github.com
     HostName github.com
     AddKeysToAgent yes
     IgnoreUnknown UseKeychain
     User git
     IdentityFile C:/Users/admin/.ssh/id_rsa
 Host test.com
     HostName github.com
     User test
     AddKeysToAgent yes
     IgnoreUnknown UseKeychain
     IdentityFile C:/Users/admin/.ssh/id_rsa_test
```
* 不同的用户配置git remote时 使用不同Host即可
  
### Could not open a connection to your authentication agent.
* 启动服务即可
``` bash
   ssh-agent bash
```

### Bad owner or permissions on .ssh/config
* 
```bash
sudo chmod 600 config
```
