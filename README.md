Docker için ekstra bir disk eklemek için aşağıdaki adımları takip edebilirsiniz. Bu adımlar, genel olarak Ubuntu Server üzerinde bir VMware sanal makinesinde disk eklemeyi içerir.

 **Yeni Diski Tanımlayın ve Biçimlendirin:**
   - Sanal makineye giriş yapın ve yeni eklenen diski tanımlayın (`lsblk` veya `fdisk -l` komutları ile).
 ![alt text](https://i.hizliresim.com/5l8vidy.jpg)
   - Diski biçimlendirin (`mkfs` veya `parted` gibi araçları kullanabilirsiniz).

```
fdisk /dev/sdb
```
```
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-41943039, default 2048): 2048
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943039, default 41943039): 41943039

Created a new partition 1 of type 'Linux' and of size 20 GiB.
```

 ![alt text](https://i.hizliresim.com/jkwramb.jpg)

```
fdisk /dev/sdb
```
```
Command (m for help): F

Unpartitioned space /dev/sdb: 20 GiB, 21473787904 bytes, 41940992 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes

Start      End  Sectors Size
 2048 41943039 41940992  20G


Command (m for help): p
Disk /dev/sdb: 20 GiB, 21474836480 bytes, 41943040 sectors
Disk model: VMware Virtual S
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x02fa0ff5
```
```
Command (m for help): q 


mkfs.ext4 /dev/sdb
mke2fs 1.46.5 (30-Dec-2021)
Creating filesystem with 5242880 4k blocks and 1310720 inodes
Filesystem UUID: d6399e92-4058-4c97-8998-0cb48ea31b2c
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

![alt text](https://i.hizliresim.com/a8loxg9.jpg)
```
mkdir /ekdisk
mount /dev/sdb /ekdisk
nano /etc/fstab
/dev/sdb   /ekdisk   ext4   defaults   0   2
```

Bu adımlar genel bir rehberdir ve VMware sürümünüze bağlı olarak detaylar değişebilir. VMware belgeleri ve kullanıcı kılavuzları, işletim sistemine ve VMware sürümüne özgü ayrıntılar içerecektir.

![alt text](https://i.hizliresim.com/7mempgc.jpg)

```
nano /etc/docker/daemon.json
```
Bu durumda, Docker, konteynerlerin ve imajların depolanacağı dizini /ekdisk/docker olarak kullanacaktır. Yaptığınız değişiklikleri kaydettikten sonra, Docker servisini yeniden başlatmayı unutmayın:
```
{
  "data-root": "/ekdisk/docker"
}
```
Bu ayarlarla birlikte, Docker artık yeni eklediğiniz diski /ekdisk/docker dizini altında kullanmaya başlayacaktır. Artık Docker üzerinde konteynerlerinizi ve imajlarınızı bu ek diskte depolayabilirsiniz.
```
systemctl restart docker
```
