# 常用软件安装
```

scoop install extras/mobaxterm

scoop install git   
//git config --global credential.helper manager  cmd下执行
scoop\apps\git\current\install-context.reg 执行添加上下文菜单 install-file-associations.reg 文件关联
tortoisegit  git路径 使用app/git/current/bin/git.exe  不要选择scoop/shims/git.exe

scoop install extras/vscode    同样注册上下文和关联文件 都在对于的app下

scoop install docker 
The 'dockerd' binary here only supports running Windows containers.
However it is possible to connect to existing Linux containers using the 'docker' binary
To register Docker as a service, run `dockerd --register-service`
Similarly, to unregister, run `dockerd --unregister-service`
scoop install main/docker-compose

scoop install extras/googlechrome
scoop install extras/chromium
scoop install extras/potplayer
scoop install extras/anydesk
```

