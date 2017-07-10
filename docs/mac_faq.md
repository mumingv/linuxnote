# FAQ

## .bashrc中设置的别名不可用，怎么处理？

新建.bash_profile文件并加载.bashrc即可，脚本如下：

```bash
$ cat .bash_profile 
if [ "${BASH-no}" != "no" ]; then  
    [ -r ~/.bashrc ] && . ~/.bashrc  
fi
```


## 如何使用SecureCRT登陆mac本机？

开启ssh服务即可：

```
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
sudo launchctl list | grep ssh
```

注意：登陆名为xxxmacBook-Pro，而不是xxxdeMacBook-Pro.local。



