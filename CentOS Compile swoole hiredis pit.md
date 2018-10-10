在centos6.8的环境下，最开始编译swoole不带任何参数，没任何问题。今天在测试swoole的异步redis。编译hiredis后，再执行swoole相关测试脚本，总提示如下错误：

```
PHP Warning:  PHP Startup: Unable to load dynamic library '/usr/local/php/lib/php/extensions/no-debug-non-zts-20160303/swoole.so' - libhiredis.so.0.13: cannot open shared object file: No such file or directory in Unknown on line 0

```

​    关于类似的问题，swoole官方文档也有相关说明，

> 
>
> ## 可能遇到的问题
>
> `php -m` 发现swoole消失或者是通过`php --ri swoole`没有显示async redis client
>
> ```
> vi ~/.bash_profile
> 在最后一行添加 export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
> source ~/.bash_profile
> ```

​    但自己遇到的问题，和官方说明不同。自己在php -m中也能找到swoole扩展。但执行swoole相关脚本就报错。最终解决方法如下。

​    （1）添加路径

```
vi ~/.bash_profile
在最后一行添加 export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
source ~/.bash_profile
```

​    （2）重新编译hireds

​    cd到hireds解压目录：（下载目录https://github.com/redis/hiredis/releases）

```
make -j
sudo make install
sudo ldconfig
```

​    （3）添加swoolehiredis.conf

```
cd /etc/ld.so.conf.d
echo "/usr/local/lib" >> swoolehiredis.conf
```

​    （4）重新编译swoole

```
./configure --enable-async-redis
make clean
make -j
sudo make install
```

​        最后重启php即可。service php-fpm reload