FROM debian:11
LABEL maintainer="yolo"

# 替换镜像源（可选）
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list

# 安装基础依赖和工具
RUN apt-get update && apt-get install -y dos2unix wget openssh-server openssh-client curl python3 python3-pip cron \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# 复制并转换脚本格式
COPY entrypoint.sh /entrypoint.sh
COPY set_default.py /set_default.py
RUN dos2unix /entrypoint.sh \
    && dos2unix /set_default.py \
    && chmod +x /entrypoint.sh \
    && chmod +x /set_default.py

# 创建必要的目录和符号链接
RUN mkdir -p /www/letsencrypt /www/init.d /www/wwwroot /run/sshd \
    && ln -s /www/letsencrypt /etc/letsencrypt \
    && mv /etc/init.d /etc/init.d.bak \
    && ln -s /www/init.d /etc/init.d

# 安装 Python 包和宝塔面板
RUN pip3 install psutil \
    && echo 'Port 63322' > /etc/ssh/sshd_config \
    && wget -O install.sh http://download.bt.cn/install/install_6.0.sh \
    && echo y | bash install.sh \
    && rm -f install.sh

# 运行设置脚本并配置宝塔面板
RUN python3 /set_default.py \
    && echo '["linuxsys", "webssh"]' > /www/server/panel/config/index.json

WORKDIR /www/wwwroot
CMD ["/entrypoint.sh"]
EXPOSE 8888 888 21 20 443 80 63322

HEALTHCHECK --interval=5s --timeout=3s CMD curl -fs http://localhost:8888/ || exit 1
