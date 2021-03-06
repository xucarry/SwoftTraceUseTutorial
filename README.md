Swoft Trace 使用教程
---------------------------------------

![文件目录格式](https://raw.githubusercontent.com/guoyahao/SwoftTraceChromeUseTutorial/master/img/ls.png)


1： 首先copy 一份最新的swoft 当做Trace server

```shell
git clone https://github.com/swoft-cloud/swoft.git  /home/SwoftTraceServer
```

修改 .env 配置文件的相关参数 不要与 开发版本想冲突 

```shell
启动命令：  php /home/SwoftTraceServer/bin/swoft ws:start -d  (一定要开启websocket)
```
```shell
打开 /home/SwoftTraceServer/app/WebSocket/  加入 SqlsController.php 
```

2： 在实际开发的版本中 引入websocket-client库  

```shell
    更新源
    composer config repo.packagist composer https://packagist.laravel-china.org
    
    引入包
    composer require swofttrace/websocket-client
    
    开始修改代码 
    swoft\vendor\swoft\console\src\Output\Output.php   >>   public function writeln() 加入代码 
```

```php
// 替换原有的$messages 变量的处理
if (\is_array($messages)) {
   $socketMessage = json_encode($messages);
   $messages = \implode($newline ? PHP_EOL : '', $messages);
}else{
   $socketMessage = json_encode(['type'=>'command','message'=>\style()->stripColor((string)$messages)]);
}
// 在echo 换行之后 加入链接 websocket-client的代码
$client = new WebSocketClient('127.0.0.1',8001,'/sqls');
$client->connect();
$client->send(json_encode($record));
$client->getSocket()->close();
```

```shell
swoft\vendor\swoft\framework\src\Log\Logger.php   >>   public function addRecord() 加入代码 
```

 ```php
 //在 foreach 函数之前加    
 $client = new WebSocketClient('127.0.0.1',8001,'/sqls');
 $client->connect();
 $client->send(json_encode($record));
 $client->getSocket()->close();
```
	重点注意的是命名空间
