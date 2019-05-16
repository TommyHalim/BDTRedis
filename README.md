# Implementasi Redis

Menggunakan 3 cluster (1 master 2 slave)

| No | IP Address | Hostname | Deskripsi | OS | RAM |
| --- | --- | --- | --- | --- | --- |
| 1 | 192.168.33.10 | master | Master | bento/Ubuntu 16.04 | 512 MB |
| 2 | 192.168.33.11 | slave1 | Slave 1 | bento/Ubuntu 18.04 | 512 MB |
| 3 | 192.168.33.12 | slave2 | Slave 2 | bento/Ubuntu 18.04 | 512 MB |



## Instalasi


### 1. Setup
Melakukan setup di masing masing node:
~~~
sudo apt-get update 
sudo apt-get install software-properties-common -y
sudo apt-get install build-essential tcl -y
~~~


### 2. Instal redis
Mendownload dan menginstal redis di setiap node menggunakan perintah:
~~~
curl -O http://download.redis.io/redis-stable.tar.gz
tar xzvf redis-stable.tar.gz
cd redis-stable
make
make test
sudo make install
~~~
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/isntallredis.JPG)



## Konfigurasi

### 1. Konfigurasi Firewall
Melakukan konfigurasi pada firewall tiap node:
~~~
sudo ufw allow 6379
sudo ufw allow 26379
sudo ufw allow from 192.168.33.10 (untuk master)
sudo ufw allow from 192.168.33.11 (untuk slave 1) 
sudo ufw allow from 192.168.33.12 (untuk slave 2)
~~~
Hal ini bertujuan untuk mengijinkan port 6379 (port redis) dan port 26379 (port sentinel) melewati firewall. Begitu pula dengan ip masing masing node


### 2. Konfigurasi Redis dan Sentinel

#### Konfigurasi redis
edit file redis.conf menjadi:
~~~
#comment line yang berisi "bind 127.0.0.1 ::1"
protected-mode no
port 6379
dir .
slaveof 192.168.33.10 6379                      #hanya untuk slave
logfile "/home/vagrant/redis-stable/redis.log"  #output file
~~~
konfigurasi slaveof menentukan node mana yang akan menjadi master.


#### Konfigurasi sentinel
edit file sentinel menjadi:
~~~
protected-mode no
port 26379
logfile "/home/redis-stable/sentinel.log"
sentinel monitor mymaster 192.168.33.10 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 10000
~~~



## Running & Testing Redis
### 1. Running Redis
Untuk menjalankan server redis dengan perintah:
~~~
sudo -u redis redis-server /etc/redis/redis.conf &
sudo -u redis redis-server /etc/redis/sentinel.conf &
~~~


### 2. Testing Redis
#### Cek Status
jalankan perintah ``ps -ef | grep redis`` untuk mengecek status
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/sf.JPG)

#### Ping dari masing masing node
dengan menggunakan perintah
~~~
redis-cli -h IP_Address_tiap_node ping
~~~
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/ping.JPG)


#### Mengecek log redis dan sentinel dari masing-masing node
Mengecek file redis.log dan sentinel.conf tiap node
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/logredis.JPG)
log redis

![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/logsentinel.JPG)
log sentinel


#### Mengecek replikasi dengan demokey
melakukan perintah ``set demokey`` untuk master dan ``get demokey`` untuk slave
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/getdemokey.JPG)


### Mengecek info replikasi tiap node
dengan menjalankan ``info replication`` pada redis-cli
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/inforepli.JPG)


#### Tes Failover
Mematikan master slave dengan perintah
~~~
redis-cli -p 6379 DEBUG SEGFAULT
~~~
![](https://github.com/TommyHalim/BDTRedis/blob/master/SS/ubahrole.JPG)
Jika master berganti pada node slave maka implementasi redis berhasil dilakukan
