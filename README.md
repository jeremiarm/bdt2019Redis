# TUGAS BDT REDIS

## Arsitektur
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_conf.png)

## Spesifikasi
Vagrant <br />
Server Redis: <br />
redis-master : <br />
OS: ubuntu-18.04 <br />
RAM: 1024 MB <br />
IP: 192.168.16.102 <br />
redis-slave-1 : <br />
OS: ubuntu-18.04 <br />
RAM: 1024 MB <br />
IP: 192.168.16.103 <br />
redis-slave-2 : <br />
OS: ubuntu-18.04 <br />
RAM: 1024 MB <br />
IP: 192.168.16.104 <br />

## Instalasi

1. Jalankan perintah dibawah untuk membuat file ``VagrantFile`` <br />
``` 
Vagrant Init 
```
2. Modifikasi file ``VagrantFile`` menjadi sebagai berikut <br />
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "redis_master" do |redis_master|
    redis_master.vm.hostname = "redis-master"
    redis_master.vm.box = "bento/ubuntu-18.04"
    redis_master.vm.network "private_network", ip: "192.168.16.102"
    
    redis_master.vm.provider "virtualbox" do |vb|
      vb.name = "redis-master"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_master.vm.provision "shell", path: "sh/redis_master.sh", privileged: false
  end

  config.vm.define "redis_slave_1" do |redis_slave_1|
    redis_slave_1.vm.hostname = "redis-slave-1"
    redis_slave_1.vm.box = "bento/ubuntu-18.04"
    redis_slave_1.vm.network "private_network", ip: "192.168.16.103"
    
    redis_slave_1.vm.provider "virtualbox" do |vb|
      vb.name = "redis-slave-1"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_slave_1.vm.provision "shell", path: "sh/redis_slave_1.sh", privileged: false
  end

  config.vm.define "redis_slave_2" do |redis_slave_2|
    redis_slave_2.vm.hostname = "redis-slave-2"
    redis_slave_2.vm.box = "bento/ubuntu-18.04"
    redis_slave_2.vm.network "private_network", ip: "192.168.16.104"
    
    redis_slave_2.vm.provider "virtualbox" do |vb|
      vb.name = "redis-slave-2"
      vb.gui = false
      vb.memory = "1024"
    end

    redis_slave_2.vm.provision "shell", path: "sh/redis_slave_2.sh", privileged: false
  end

end

```

3. Buat file `` redis_master.sh `` dengan isi <br />
```
# Copy APT sources list
sudo cp /vagrant/sources/sources.list /etc/apt/

sudo apt-get update -y

sudo apt-get install redis -y

sudo cp /vagrant/conf/redis_master.conf /etc/redis/redis.conf
sudo cp /vagrant/conf/redis_master_sentinel.conf /etc/redis/sentinel.conf

```

4. Buat file `` redis_slave_1.sh `` dengan isi <br />
```
# Copy APT sources list
sudo cp /vagrant/sources/sources.list /etc/apt/

sudo apt-get update -y

sudo apt-get install redis -y

sudo cp /vagrant/conf/redis_slave_1.conf /etc/redis/redis.conf
sudo cp /vagrant/conf/redis_slave_1_sentinel.conf /etc/redis/sentinel.conf

```

5. Buat file `` redis_slave_2.sh `` dengan isi <br />
```
# Copy APT sources list
sudo cp /vagrant/sources/sources.list /etc/apt/

sudo apt-get update -y

sudo apt-get install redis -y

sudo cp /vagrant/conf/redis_slave_2.conf /etc/redis/redis.conf
sudo cp /vagrant/conf/redis_slave_2_sentinel.conf /etc/redis/sentinel.conf

```

6. Buat file konfigurasi `` redis_master.conf`` dengan isi <br />
```
bind 192.168.16.102
port 6380

dir "/etc/redis"
```
7. Buat file konfigurasi `` redis_master_sentinel.conf`` dengan isi <br />
```
# Host and port we will listen for requests on
bind 192.168.16.102
port 16380

#
# "redis-cluster" is the name of our cluster
#
# each sentinel process is paired with a redis-server process
#
sentinel monitor redis-cluster 192.168.16.102 6380 2
sentinel down-after-milliseconds redis-cluster 5000
sentinel parallel-syncs redis-cluster 1
sentinel failover-timeout redis-cluster 10000
```
8. Buat file konfigurasi `` redis_slave_1.conf`` dengan isi <br />
```
bind 192.168.16.103
port 6380

dir "/etc/redis"

slaveof 192.168.16.102 6380
```
9. Buat file konfigurasi `` redis_slave_1_sentinel.conf`` dengan isi <br />
```
# Host and port we will listen for requests on
bind 192.168.16.103
port 16380

#
# "redis-cluster" is the name of our cluster
#
# each sentinel process is paired with a redis-server process
#
sentinel monitor redis-cluster 192.168.16.102 6380 2
sentinel down-after-milliseconds redis-cluster 5000
sentinel parallel-syncs redis-cluster 1
sentinel failover-timeout redis-cluster 10000
```

10. Buat file konfigurasi `` redis_slave_2.conf`` dengan isi <br />
```
bind 192.168.16.104
port 6380

dir "/etc/redis"

slaveof 192.168.16.102 6380
```
11. Buat file konfigurasi `` redis_slave_2_sentinel.conf`` dengan isi <br />
```
# Host and port we will listen for requests on
bind 192.168.16.104
port 16380

#
# "redis-cluster" is the name of our cluster
#
# each sentinel process is paired with a redis-server process
#
sentinel monitor redis-cluster 192.168.16.102 6380 2
sentinel down-after-milliseconds redis-cluster 5000
sentinel parallel-syncs redis-cluster 1
sentinel failover-timeout redis-cluster 10000
```

12. Jalankan `` vagrant up`` <br />
13. Jalankan `` vagrant ssh redis_master `` dan jalankan `` sudo redis-server /etc/redis/redis.conf `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_master_1.jpg)
14. Buka cmd baru ,kemudian jalankan `` vagrant ssh redis_master `` dan jalankan `` sudo redis-server /etc/redis/sentinel.conf --sentinel `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_master_2.jpg)
15. Buka cmd baru, kemudian jalankan `` vagrant ssh redis_slave_1 `` dan jalankan `` sudo redis-server /etc/redis/redis.conf `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_slave1_1.jpg)
16. Buka cmd baru ,kemudian jalankan `` vagrant ssh redis_slave_1 `` dan jalankan `` sudo redis-server /etc/redis/sentinel.conf --sentinel `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_slave1_2.jpg)
17. Buka cmd baru, kemudian jalankan `` vagrant ssh redis_slave_2 `` dan jalankan `` sudo redis-server /etc/redis/redis.conf `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_slave2_1.jpg)
18. Buka cmd baru ,kemudian jalankan `` vagrant ssh redis_slave_2 `` dan jalankan `` sudo redis-server /etc/redis/sentinel.conf --sentinel `` <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_slave2_2.jpg)
19. Cek kembali ke redis_master pada perintah "13", akan muncul hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/redis_master_3.jpg)
20. Buka cmd baru, masuk ke salah satu server dan jalankan untuk menemukan address master masing-masing server <br />
```
redis-cli -h 192.168.16.102 -p 16380 sentinel get-master-addr-by-name redis-cluster
redis-cli -h 192.168.16.103 -p 16380 sentinel get-master-addr-by-name redis-cluster
redis-cli -h 192.168.16.104 -p 16380 sentinel get-master-addr-by-name redis-cluster
```
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/getmaster_redis.jpg) <br />
21. Jalankan `` redis-cli -h 192.168.16.102 -p 6380 `` , kemudian jalankan `` info replication `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/master_info_replication.jpg) <br />
22. Jalankan `` redis-cli -h 192.168.16.103 -p 6380 `` , kemudian jalankan `` info replication `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/slave1_info_replication.jpg) <br />
23. Jalankan `` redis-cli -h 192.168.16.104 -p 6380 `` , kemudian jalankan `` info replication `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/slave2_info_replication.jpg) <br />
24. Jalankan `` redis-cli -h 192.168.16.102 -p 16380 `` , kemudian jalankan `` info sentinel `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/master_sentinel.jpg) <br />
25. Jalankan `` redis-cli -h 192.168.16.103 -p 16380 `` , kemudian jalankan `` info sentinel `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/slave1_sentinel.jpg) <br />
26. Jalankan `` redis-cli -h 192.168.16.104 -p 16380 `` , kemudian jalankan `` info sentinel `` sehingga hasil seperti gambar <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/slave2_sentinel.jpg) <br />

# Wordpress
1. Install Jmeter terlebih dahulu dengan tutorial https://www.blazemeter.com/blog/how-get-started-jmeter-part-1-installation-test-plans/ <br />
2. Web server menggunakan local dengan enable plugin dan disable plugin saat testing. <br />
3. Instalasi wordpress pada xampp dapat diikuti tutorialnya pada https://www.fixrunner.com/wordpress-localhost-installation/ <br />
tambahkan pada `` wp-config.php `` <br />
```
define('WP_REDIS_CLIENT', 'predis');
define('WP_REDIS_SENTINEL', 'redis-cluster');
define('WP_REDIS_SERVERS',[
	'tcp://192.168.16.102:16380',
	'tcp://192.168.16.103:16380',
	'tcp://192.168.16.104:16380',
]);
```
4. Uji coba dilakukan pada `` /redistesting/2019/11/24/hello-world/ `` <br />
5. Uji coba dengan 50 koneksi redis enabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_50_enabled.jpg) <br />
6. Uji coba dengan 50 koneksi redis disabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_50_disabled.jpg) <br />
7. Uji coba dengan 202 koneksi redis enabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_202_enabled.jpg) <br />
8. Uji coba dengan 202 koneksi redis disabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_202_disabled.jpg) <br />
9. Uji coba dengan 302 koneksi redis enabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_302_enabled.jpg) <br />
10. Uji coba dengan 302 koneksi redis disabled <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/jmeter_302_disabled.jpg) <br />
## Kesimpulan
Dilihat dari uji coba di atas, disimpulkan bahwa wordpress dengan plugin Redis Object Cache lebih lambat daripada tidak menggunakan plugin Redis Object Cache. Hal ini mungkin terjadi karena jumlah permintaan ke server redis lebih banyak dari pada jumlah permintaan ke mysql sehinga menyebabkan overheat pada server redis. Hal ini mungkin dapat diatasi dengan menambah RAM pada server redis, di mana saat ini hanya menggunakan 1 GB RAM pada tiap server redis <br /> <br />

# FAILOVER
1. Masuk ke salah satu server, kemudian jalankan <br />
```
redis-cli -h 192.168.16.102 -p 6380 debug segfault
```
2. Kemudian cek pada proses "13" (redis_master) <br />
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/failover_proses.jpg) <br />
3. Kemudian pada masing-masing server jalankan <br />
```
redis-cli -h 192.168.16.102 -p 16380 sentinel get-master-addr-by-name redis-cluster
redis-cli -h 192.168.16.103 -p 16380 sentinel get-master-addr-by-name redis-cluster
redis-cli -h 192.168.16.104 -p 16380 sentinel get-master-addr-by-name redis-cluster
```
![alt text](https://github.com/jeremiarm/bdt2019Redis/blob/master/screenshot/new_master.jpg) <br />
4. Dari gambar di atas, slave_1 otomatis menjadi master baru <br />
