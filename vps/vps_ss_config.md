vi /etc/rc.local
/usr/bin/ssserver -c /etc/shadowsocks.json

/etc/shadowsocks.json
{
        "server":"45.62.102.9",
        "server_port":9001,
        "local_address":"127.0.0.1",
        "local_port":1080,
        "password":"7924677",
        "timeout":300,
        "method":"aes-256-cfb",
        "fast_open":false,
        "workers":1
}

yum -y install wget;wget http://download.kanglesoft.com/easypanel/ep.sh -O ep.sh;
sh ep.sh

mysqladmin -u root password 123456

