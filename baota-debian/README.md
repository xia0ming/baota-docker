docker build --platform linux/amd64,linux/arm64 .
docker images
docker tag [image_id] iyhm/baota-debian:latest
docker login
docker push iyhm/baota-debian:latest

# 参数
docker run
  -d
  --name='baota_debian'
  --net='br0'
  --ip='192.168.5.7'
  -e TZ="Asia/Shanghai"
  -l net.unraid.docker.icon='https://www.bt.cn/static/images/bt_logo_new.png'
  -v '/mnt/user/appdata/baota':'/www':'rw'
  -v '/mnt/disk2/Unraid/projects/':'/wwwroot':'rw' 'yanghongming/yolo:clear'

docker run -d --name='baota_debian' --net='br0' --ip='192.168.5.7' -e TZ="Asia/Shanghai" -e HOST_OS="Unraid" -e HOST_HOSTNAME="yolo" -e HOST_CONTAINERNAME="baota_debian" -l net.unraid.docker.managed=dockerman -l net.unraid.docker.webui='http://192.168.5.7:8888' -l net.unraid.docker.icon='https://www.bt.cn/static/images/bt_logo_new.png' -v '/mnt/user/appdata/baota':'/www':'rw' -v '/mnt/disk2/Unraid/projects/':'/wwwroot':'rw' 'yanghongming/yolo'

# /www宝塔面板程序目录持久化
/www为宝塔面板程序目录(内部包括/sever软件安装目录等), 如果想持久化, 第一次运行docker时不要映射/www, 因为映射的/www没有系统需要的文件会报错
需要先将宿主机持久化目录如/appdata/baota_debian映射到容器内临时目录如/www1 (容器内会自动'新建'/www1)
等docker成功运行后, 在容器中将/www内所有文件复制到/www1 (也就是复制到了宿主机/appdata/baota_debian内)
然后重新拉取容器, 并添加/appdata/baota_debian => /www的映射, 实现/www文件的持久化

## 宝塔使用问题
# node: command not found
安装nodejs后, 命令行执行node报错: node: command not found, 类似的还有npm/npx等
ln -s /www/server/nodejs/v20.13.1/bin/node /usr/local/bin/node

# phpmyadmin 无法访问, 访问变成下载, phpmyadmin切换php版本不成功
卸载phpmyadmin, 重新安装

