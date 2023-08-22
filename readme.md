```
lsblk --output NAME,FSTYPE,LABEL,UUID,MODE

ls -l /dev/disk/by-id/
```

```
# set vm id
id=101

# create image directory, download and uncomporess
mkdir -p /var/lib/vz/images/${id}
curl --location https://github.com/pocopico/tinycore-redpill/releases/download/v0.9.4.0/tinycore-redpill.v0.9.4.0.img.gz --output /var/lib/vz/images/${id}/tinycore-redpill.img.gz
gzip --decompress /var/lib/vz/images/${id}/tinycore-redpill.img.gz --keep

# create disk for sata0
pvesm alloc local-lvm ${id} vm-${id}-disk-0 25G

# create vm
qm create ${id} \
  --args "-drive 'if=none,id=synoboot,format=raw,file=/var/lib/vz/images/${id}/tinycore-redpill.img' -device 'qemu-xhci,addr=0x18' -device 'usb-storage,drive=synoboot,bootindex=1'" \
  --cores 6 \
  --cpu kvm64 \
  --machine q35 \
  --memory 8192 \
  --name DSM7 \
  --net0 virtio,bridge=vmbr0 \
  --numa 0 \
  --onboot 0 \
  --ostype l26 \
  --scsihw virtio-scsi-pci \
  --sockets 1 \
  --serial0 socket \
  --serial1 socket \
  --tablet 1
qm set ${id} -sata0 /dev/disk/by-id/ata-WDC_WD2004FBYZ-01YCBB2_WD-WMC6N0J25W0J
qm set ${id} -sata1 /dev/disk/by-id/ata-Samsung_SSD_850_EVO_1TB_S2RENX0J700317F,ssd=1
qm set ${id} -sata2 /dev/disk/by-id/ata-Samsung_SSD_860_EVO_1TB_S4FMNF0NB07207L,ssd=1
qm set ${id} -sata3 /dev/disk/by-id/ata-WDC_WD20EFRX-68EUZN0_WD-WCC4M7HZUSR7
```

```
Login: tc
Password: P@ssw0rd
ssh ts@IP

./rploader.sh update now
./rploader.sh fullupgrade now
./rploader.sh satamap now
./rploader.sh identifyusb now
./rploader.sh serialgen ds3622xs+
./rploader.sh ext ds3622xsp-7.2.0-64570 add https://github.com/pocopico/tcrp-addons/raw/main/all-modules/rpext-index.json
./rploader.sh ext ds3622xsp-7.2.0-64570 add https://github.com/pocopico/tcrp-addons/raw/main/eudev/rpext-index.json
./rploader.sh backup
./rploader.sh build ds3622xsp-7.2.0-64570 withfriend

open and check user_config.json

sudo reboot -f
```

https://xpenology.com/forum/topic/65363-установка-dsm-711-на-proxmox/
