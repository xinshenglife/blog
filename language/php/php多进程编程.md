https://itlanyan.com/php-review-multi-process/
https://blog.csdn.net/weixin_34835735/article/details/116179569 遇到的问题和我类似
最后结果使用proc_open   
stream_set_blocking函数可以将资源流设置为阻塞模式或者非阻塞模式，主要影响的函数分别是fgets,fread
```
$parentId = posix_getpid();
fwrite(STDOUT, "my pid: $parentId\n");
$childNum = 1;
foreach (range(1, $childNum) as $index) {
    $pid = pcntl_fork();
    if ($pid === -1) {
        fwrite(STDERR, "failt to fork!\n");
        exit;
    }
    // parent code
    if ($pid > 0) {
        fwrite(STDOUT, "fork the {$index}th child, pid: $pid\n");
        //父进程代码
        $wait_result=pcntl_wait($status); //如果没有这个子进程会变成僵尸进程--这个是阻塞的
        //$wait_result= pcntl_waitpid($pid, $status,WNOHANG) //不阻塞
        fwrite(STDOUT, "回收进程".$wait_result);
    } else {
        $mypid = posix_getpid();
        $parentId = posix_getppid();
        fwrite(STDOUT, "I'm the {$index}th child and my pid: $mypid, parentId: $parentId\n");
        //sleep(5);
        $this->handleRun();//主要任务
        exit;               // 注意这一行
    }
}
```
除了fork，另外一种多进程技术是exec。   ----实际就是启动sh 脚本然后proc_get_status 得到的是sh的进程信息 得不到exec执行的命令的进程信息  是因为命令后面有 & 会产生子进程    去掉……& 后就正常了。shell命令不会产生子进程 &还会导致shell的产生的子进程父ID是 1 变成守护进程
参考https://zhuanlan.zhihu.com/p/543308214
system、
exec、
proc_open等函数会生成一个新的进程执行外部命令（并返回结果）。这些函数的本质是fork一个进程，然后调用shell执行命令，主进程等待其执行结束。函数执行期间，主进程除了等待无法处理其他任务，所以一般不认为这是多进程编程

proc_open 实际过程中好像并不会等待 又该如何回收避免僵尸进程
proc_terminate() 允许终止进程并继续其他的任务。可以使用 
proc_get_status() 函数轮询进程（查看是否已经停止
读写了proc_open pipe管道的数据  需要在proc_close之前及时关闭使用pclose 关闭输入输出流
proc_get_status()如果调用成功，则返回一个包含了进程信息的 array，如果发生错误，返回 FALSE。 返回的数组包含下列元素：
private static $process=[]; 
```
//$cmd
self::$process[$task_id] = proc_open($cmd, $descriptorspec, $pipes);
$status=proc_get_status(self::$process[$task_id]); //获取状态
//监控最终采用了
$cmd="ps -eo pid,etime,args |grep ffmpeg| grep ".$pull_live_url."|grep ".$push_live_url."|grep -v grep";
//得到进程pid和时长

//proc_close($process);  //关闭进程的时候关闭
//安全杀死对应的进程 不会产生僵尸进程--下面分开就会导致僵尸或者其他异常
proc_terminate(self::$process[$task_id]); 
//关闭对应的进程 允许终止进程并继续其他的任务
proc_close(self::$process[$task_id]);
//proc_close()关闭由 proc_open 打开的进程并且返回进程退出码
```