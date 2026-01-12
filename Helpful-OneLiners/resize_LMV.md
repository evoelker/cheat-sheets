## URL's:
http://ryandoyle.net/posts/expanding-a-lvm-partition-to-fill-remaining-drive-space/
https://fabianlee.org/2016/07/26/ubuntu-extending-a-virtualized-disk-when-using-lvm/


1. Add space to VMDK
2. Force the Ubuntu Kernel to rescan the device:
```bash
echo 1 > /sys/class/block/sda/device/rescan
```
3. Resize the partition:
```bash
fdisk /dev/<HDD>

# Optional if installed from cloud-guest-utils
growpart /dev/<HDD> <HDD Partition>
```
4. Resize the LV physicals disk (note: this updates the LVG): 
```bash
pvresize /dev/<HDD> -y -v
```
5. Resize the LV
```bash
lvresize -l +100%FREE --resizefs /dev/ubuntu-vg/ubuntu-lv
```
