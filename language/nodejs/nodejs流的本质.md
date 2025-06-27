 
 # 就是固定的buff 批量处理数据 否则不叫流
 websocket 或者http 服务很多 读写流都是保护.所以只能使用它的方法发送  管道 stdin stdout stderr 本质都是读写虚拟的文件  然后buff
 # 正常大文件读取  分批读取读取发送出去
 ```
 function readFile(filePathName, fileStartPostion, pageSize = 2048) {
    const fd = fs.openSync(filePathName, 'r+');
    let readBuffer = Buffer.alloc(pageSize);
    let offset = 0;
    return fs.readSync(fd, readBuffer, offset, pageSize, fileStartPostion).toString();
}
 let totalLen=1024*2;
    let startPos=0;  //lastTotalLen <= totalLen情况
    if(lastTotalLen > totalLen){
        startPos=lastTotalLen-totalLen;
    }
    let pageSize=2048;
    let size =Math.ceil(totalLen/pageSize);
    for(page=0;page<size;page++){
        let fileStartPostion = startPos+page*pageSize; //开始读取的位置
        let data = await readFile(filePathName_err,fileStartPostion,pageSize);
        console.log("输出错误日志",filePathName_err,data,fileStartPostion);
        if(ws.readyState===1){
            ws.send(data);
        } 
    }
```
# stream处理 
```
filePathName_err=filePath+"/stderr.log";
let data='';
let readerStream = fs.createReadStream(filePathName_err);
// 设置编码为 utf8。
readerStream.setEncoding('UTF8');
// 处理流事件 --> data, end, and error
readerStream.on('data', function(chunk) {
    //data += chunk;
    ws.send(chunk);
});
readerStream.on('end',function(){
    //console.log(data);
    ws.send("end");
    //throw new Error('读取日志完成');
});
readerStream.on('error', function(err){
    console.log(err.stack);
    throw new Error('错误日志文件不存在');
});
 ```       

 可以看到本质是一样的。stream有个参数可以设置buff的大小。只不过stream 更规范 推荐stream