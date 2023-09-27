# linux frp

frp 下载地址
https://github.com/fatedier/frp/releases

### frps 命令
```shell
1.‘./frps’: Permission denied
chmod 777 frps
ps -elf

2.后台运行frp：
nohup ./frps -c frps.ini &

3.开机自启动：
sudo mkdir -p /etc/frp
sudo cp frps.ini /etc/frp
sudo cp frps /usr/bin
sudo cp systemd/frps.service /usr/lib/systemd/system/
sudo systemctl enable frps
sudo systemctl start frps
```

### frpc 命令
```shell
1.‘./frpc’: Permission denied
chmod 777 frpc

2.后台运行frp：
nohup ./frpc -c frpc.ini &

3.开机自启动：
sudo mkdir -p /etc/frp
sudo cp frpc.ini /etc/frp
sudo cp frpc /usr/bin
sudo cp systemd/frpc.service /usr/lib/systemd/system/
sudo systemctl enable frpc
sudo systemctl start frpc
```