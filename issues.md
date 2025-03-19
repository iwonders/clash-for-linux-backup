1.  start.sh 有语法错误，第154行末尾少了一个 `\`,这导致155行没有运行成功，proxy.txt没有写入 proxies 信息，进而导致 conf/config.yaml 也没有 proxies 信息，导致面板看不到 vpn list，于是使用vpn使用失败；
```sh
153:    -e '/^log-level:/d'\
154:    -e '/^external-controller:/d'
155:    -e '/^secret:/d' > $Temp_Dir/proxy.txt
```
`154:    -e '/^external-controller:/d'` 要修正为 154:    -e '/^external-controller:/d\'

2. 如果出现Unauthorized，一般是配置文件有问题。注意：1修复了如果直接运行 start.sh ， 第二个坑来了，因为第一个我们已经在 conf/config.yaml 和 /root/clash/temp/ 中除了templete_config.yaml 写入了信息，这些里面的内容都需要清除掉，不然第二次写入信息会造成新的内容问题。
```sh
cat /dev/null > /root/clash/conf/config.yaml
cat /dev/null > /root/clash/temp/clash_config.yaml
cat /dev/null > /root/clash/temp/clash.yaml
cat /dev/null > /root/clash/temp/config.yaml
cat /dev/null > /root/clash/temp/proxy.txt
```
也就是说每次运行 start.sh 都要将这些文件内容清空，相当于清理缓存一样。不然你永远找不到问题所在，永远运行失败。

3.就是clash面板一不小心叉掉了 127.0.0.1 面板一直进不去了，访问会出现Oops, something went wrong!。这是第三个坑，这个时候浏览器打开开发者(F12)清理掉 应用->local storate下面的内容，或者右键地址栏左边的刷新按钮后点击“清空缓存并硬性重新加载”，然后再进去。
引自 “https://github.com/Elegycloud/clash-for-linux-backup/issues/87”

4.好了前面坑解决后，服务终于启动。此时clash进程异常卡顿，导致终端掉线，浏览器也无法访问。连续几次 start.sh 也是一样。
最后自己研究 start.sh ，自己手动启动 :
clash会造成卡顿掉线，进程自动被杀死，之后不要用 start.sh启动，手动启动即可：
`nohup /root/clash/bin/clash-linux-amd64 -d /root/clash/conf &> /root/clash/logs/clash.log &`
手动启动，可以正常打开面板，不会有问题了。
👍  完美
