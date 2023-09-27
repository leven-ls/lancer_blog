

```bash
# 检查是否已经安装 redis
redis-server -v
if [ $? -eq 0 ]; then
    echo "redis 已经安装"
else
    echo "redis 未安装"
    # 安装 redis
    sudo apt-get install redis-server
    # 启动 redis
    sudo service redis-server start
    # 查看 redis 状态
    sudo service redis-server status
    # 查看 redis 端口
    netstat -an | grep 6379
fi
```