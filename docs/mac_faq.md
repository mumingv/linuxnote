# FAQ

## .bashrc中设置的别名不可用，怎么处理？

新建.bash_profile文件并加载.bashrc即可，脚本如下：

```bash
$ cat .bash_profile 
if [ "${BASH-no}" != "no" ]; then  
    [ -r ~/.bashrc ] && . ~/.bashrc  
fi
```

