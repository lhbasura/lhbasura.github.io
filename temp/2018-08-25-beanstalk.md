---  
layout:     post
title:      beanstalk笔记
subtitle:   beanstalk
date:       2019-08-27
author:     
header-img: 
keywords_post:  "beanstalk"
catalog: true
tags:
    - beanstalk  
---    

在队列生产、和队列消费前都需要执行队列管理选择命令：
use  use命令就像MYSQL里的USE一个数据库一样(实际上beanstalkd也可理解为一个队列集合，里面每个tube是一个队列，use命令在这些队列间切换)。

## Producer 队列生产命令
put   put命令用来向队列中插入一个job，包括一个命令行和一个job体：    
put <pri> <delay> <ttr> <bytes>  <data>   put 命令将向客户端现在正在使用的tube中插入一个job。  
<pri> 是一个小于2**32的整数。小优先级数值的job将会排在大优先级 数值的job前面执行。最高优先级是0，最低优先级是4,294,967,295。 
<delay> 是一个整形数，表示将job放入ready队列需要等待的秒数。
<ttr> --time to run—是一个整形数，表示允许一个worker执行该job的秒 数。这个时间将从一个worker 获取一个job开始计算。如果该worker没能在<ttr> 秒内删除、释放或休眠该job，这个job就会超时，服务端会主动释放该job。最小ttr为1。如果客户端设置了0，服务端会默认将其增加到1。
<bytes> 是一个整形数，表示job体的大小，不包括结尾的。这个 值不能大于max-job-size（默认值为2**16）。  
<data> 及job体，是一个长度为<byetes> 的字符序列。 在发送命令行和job体之后，客户端等待如下的响应：  
"INSERTED <id>" 表示成功。<id> 是新job的数字编号。  
"BURIED <id>" 表示服务端没有足够的内存将job添加到优先级队列 中。<id> 是新job的数字编号。  
"EXPECTED_CRLF" 表示该job必须以CR-LF对，及  结尾。这 两个字节在客户端的put命令中将不会被计算到job大小中。  
"JOB_TOO_BIG" 表示客户端发送一个超过max-job-size字节的job体。 
"DRAINING" 表示服务端已进入drain mode，将不会再接收新的job。 客户端应该超时其他的服务端或者断开连接稍后重试。

Comsumer 队列消费命令
进程可以通过reserve, delete, release和bury 四个命令来从队列里面消耗job。   
reserve 或者reserve-with-timeout <seconds> (可以指定一个超时时间)。beanstalkd将一直等待发送响应，直到一个job可用。
当一个job被客户端获取，客户端必须在TTR时间内完成该job，否则job就会超时。当该job超时，服务端会将该job重新放回ready队列。TTR和当前剩余时间都可以在stats-job命令的响应中找到。如果超时设置为0，服务端将立刻返回一个响应或者TIME_OUT。如果超时是一个大于0的值，客户端将会在获取job请求中阻塞，直到一个job可用或者超时。对于一个已经被获取job的TTR，最后一秒是作为安全边际被服务端保留的，在此期间，客户端将无法试图去等待别的job。如果客户端在安全边际期间发送获取job命令，或者安全边际到来时还在等待获取命令的完成，服务端将返回：
DEADLINE_SOON  这给了客户端一个在服务端自动发布该job之前，删除或者重新发布已经获取job的机会。  
TIMED_OUT当指定了一个非负的超时时间值，并且在超时时或没有一个job可用，服务端将返回TIMED_OUT。 否则，这条命令唯一成功的响应形式是一个文本行后跟一个job体。  
RESERVED <id> <bytes>  <data>  
    <id> job的id，在这个beanstalkd的实例中，代表该job的唯一整数。 
    <bytes> 表示job体大小的整数，不包括结尾的 。 
    <data> 表示消息体，一个长度为 <bytes> 的字节序列。这是对之前put 命令job体原始字节的逐字拷贝。  

delete  delete命令从服务端完全删除一个job。当客户端已经成功执行完一个job时，该job一般会被使用。客户端可以删除一个已经被获取的job，可用的job，以及被休眠的job。delete命令如下：   delete <id>  <id> 表示要删除的job id。 客户端将等待如下的响应行：  
"DELETED" 表示已经成功删除。  
"NOT_FOUND" 表示该job不存在，或者该job没有被客户端获取， 已经不在ready或buried状态。这种情况发生这客户端发送delete命令之前，该job已经超时。  

release  release命令将一个已经被获取的job重新放回ready队列（并将job状态置为 ready），让该job可以被其他客户端执行。这个命令经常在job因为短暂的错误而失败时使用。格式如下：  
release <id> <pri> <delay> 
<id> 表示要release的job id。  
<pri> 表示给该job分配的新的优先级。  
<delay> 表示在该job被放入ready队列之前需要等待的秒数。在此期间， job的状态将是delayed。 客户端期待的响应如下： 
"RELEASED" 表示成功  
"BURIED" 表示服务端没有足够的内存将job添加到优先级队列中。 
"NOT_FOUND" 表示该job不存在或没有被客户端获取.

bury  bury命令将一个job的状态置为buried。Buried job被放在一个FIFO的链表中，在客户端调用kick命令之前，这些job将不会被服务端处理。  
bury命令格式如下：  bury <id> <pri> <id> 表示该job的id  <pri> 表示分配给该job新的优先级 有两种可能的响应：  
"BURIED" 表示成功  
"NOT_FOUND" 表示该job不存在或没有被客户端获取。 

touch  touch命令允许一个worker请求在一个job获取更多执行的时间。这对于那些需要长时间完成的job是非常有用的，但同时也可能利用TTR的优势将一个job从一个无法完成工作的worker处移走。一个worker可以通过该命令来告诉服务端它还在执行该job（比如：在收到DEADLINE_SOON是可以发生给命令）。  
touch命令格式如下：  touch <id>  <
id> 表示当前连接获取job的id 有两种可能的响应：  
"TOUCHED" 表示成功  "NOT_FOUND" 表示该job不存在或没有被客户端获取。 

watch  watch命令将一个tube名称加入当前连接的watch list。reserve命令将从watch list里面的任意一个tube中获取job。对于每个新建的连接，watch list中只有一个default tube。  watch <tube>  <tube> 是一个不超过200字节的名称。它指定将一个tube加入到watch  list。如果该tube不存在，将会被及时创建。 响应是：
WATCHING <count>  <count> 表示当前watch list中的tube数目。  

ignore  ignore命令是为consumer设计的。ignore命令将一个tube从当前连接的watch list中移除。   ignore <tube> 响应如下：  "WATCHING <count>" 表示成功。<count>表示当前watch list中的 tube数目。  "NOT_IGNORED" 表示客户端正在试图ignore watch list中的最后一 个tube。 

kick  kick命令只能针对当前正在使用的tube执行。它将buried或者delayed状态的job移动到ready队列。命令格式如下：   
kick <bound>  
<bound> 表示每次kick job的上限，服务端将最多kick <bound>个job。 响应格式如下：  
KICKED <count>  <count> 表示本次kick操作作用job的数目。  
peek peek命令可以让客户端检查系统中的job。这个命令有4个变种。除第一个命令之外，其他3个命令都只能作用在当前使用的tube上面。  "peek <id>" 返回编号为<id>的job。  
"peek-ready" 返回当前tube下一个ready的job。  
"peek-delayed" 返回当前tube剩余delay时间最短的job。 
"peek-buried" 返回当前tube buried list中下一个job。 有两种可能的响应，其中一个是单行：  
"NOT_FOUND" 表示请求的job不存在，或者没有job在当前请求的状态队列中。  
还有一个是一行跟一个块数据，表示命令执行成功：  
FOUND <id> <bytes> <data>  <id> 表示job的id。  
<bytes> 表示job体大小的整数，不包括结尾的 。 
<data> 表示消息体，一个长度为 <bytes> 的字节序列。  

其它命令：
list-tubes          返回所有tube列表
list-tube-used      返回当前正在使用的tube
list-tubes-watched  返回当前正在关注的tube名称列表。
pause-tube          用来在给定时间内暂停从tube获取job。
    pause-tube <tube-name> <delay>
    <tube> 表示要暂停的tube。
    <delay> 表示在可以从tube获取job之前需要等待的秒数。 
    有两种可能的响应：
    "PAUSED" 表示成功。  "NOT_FOUND" 表示该tube不存在。  
quit   quit命令用来关闭当前连接。
stats-job 查看job的信息，
stats-tube 查看tube的信息
stats  查看beanstalk的信息
