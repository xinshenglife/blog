包依赖管理工具


用户/项目/本地依赖      依赖顺序  本地--项目--用户--系统-全局



按照用户划分
/sys/bin   
/sys/lib      bin 是二进制   bin lib 是对应操作系统

boot 启动加载内核 
sys内核就启动相关系统服务 进程 存储 用户  交互(GUI  CLI-shell)  网络
root 管理员 可以更改系统设置
user1 一般用户使用正常app 但不能更改系统设置

/boot/projects/project1/src  
/boot/projects/project1/src/index.各种编程语言扩展名  入口文件-------然后这个用户最先启动的项目1的index 也就是用户启动
/boot/projects/project1/src/vendor  就是依赖库--或者更换个名字   
            
/sys/projects/project1/src  
/sys/projects/project1/src/index.各种编程语言扩展名  入口文件-------然后这个用户最先启动的项目1的index 也就是用户启动
/sys/projects/project1/src/vendor  就是依赖库--或者更换个名字   
 

/root/projects/project1/src  
/root/projects/project1/src/index.各种编程语言扩展名  入口文件-------然后这个用户最先启动的项目1的index 也就是用户启动
/root/projects/project1/src/vendor  就是依赖库--或者更换个名字

/user1/projects/project1/src  
/user1/projects/project1/src/index.各种编程语言扩展名  入口文件-------然后这个用户最先启动的项目1的index 也就是用户启动
/user1/projects/project1/src/vendor  就是依赖库--或者更换个名字
