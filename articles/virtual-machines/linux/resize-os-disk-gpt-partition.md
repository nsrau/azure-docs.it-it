---
title: Ridimensionare un disco del sistema operativo con una partizione GPT | Microsoft Docs
description: Questo articolo fornisce istruzioni sul ridimensionamento di un disco del sistema operativo con una partizione GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 30a960c3ed76788158b15022947fec49a95ae299
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375211"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ridimensionare un disco del sistema operativo con una partizione GPT

> [!NOTE]
> Questo scenario si applica solo ai dischi del sistema operativo con una partizione GPT (tabella di partizione GUID, GUID Partition Table).

Questo articolo descrive come aumentare le dimensioni di un disco del sistema operativo con una partizione GPT in Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Determinare se il disco del sistema operativo ha una partizione MBR o GPT

Usare il `parted` comando per stabilire se la partizione disco è stata creata con una partizione MBR (master boot record) o una partizione GPT.

### <a name="mbr-partition"></a>Partizione MBR

Nell'output seguente **Partition Table** mostra il valore **msdos**. Questo valore identifica una partizione MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Partizione GPT

Nell'output seguente **Partition Table** mostra il valore **gpt**. Questo valore identifica una partizione GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Se il disco del sistema operativo della macchina virtuale (VM) ha una partizione GPT, aumentarne le dimensioni.

## <a name="increase-the-size-of-the-os-disk"></a>Aumentare le dimensioni del disco del sistema operativo

Le istruzioni seguenti si applicano a distribuzioni approvate da Linux.

> [!NOTE]
> Prima di procedere, creare una copia di backup della macchina virtuale o acquisire uno snapshot del disco del sistema operativo.

### <a name="ubuntu"></a>Ubuntu

Per aumentare le dimensioni del disco del sistema operativo in Ubuntu 16.x e 18.x:

1. Arrestare la VM.
1. Aumentare le dimensioni del disco del sistema operativo dal portale.
1. Riavviare la macchina virtuale e quindi accedervi come utente **radice**.
1. Verificare che il disco del sistema operativo ora visualizzi dimensioni del file system aumentate.

Come illustrato nell'esempio seguente, il disco del sistema operativo è stato ridimensionato a 100 GB dal portale. Il file system **/dev/sda1** montato su **/** ora visualizza 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Per aumentare le dimensioni del disco del sistema operativo in SUSE 12 SP4, SUSE SLES 12 per SAP, SUSE SLES 15 e SUSE SLES 15 per SAP:

1. Arrestare la VM.
1. Aumentare le dimensioni del disco del sistema operativo dal portale.
1. Riavviare la VM.

Una volta riavviata la VM, seguire questa procedura:

1. Accedere alla macchina virtuale come utente **radice** usando il comando seguente:

   ```
   # sudo -i
   ```

1. Usare il comando seguente per installare il pacchetto **growpart** , che verrà usato per ridimensionare la partizione:

   ```
   # zypper install growpart
   ```

1. Usare il `lsblk` comando per trovare la partizione montata nella radice del file System ("/"). In questo caso, si noterà che la partizione 4 del dispositivo SDA è montata in/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Ridimensionare la partizione necessaria usando il `growpart` comando usando il numero di partizione trovato nel passaggio precedente.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Eseguire `lsblk` di nuovo il comando per verificare se la partizione è stata aumentata.

   L'output seguente mostra che la partizione **/dev/sda4** è stata ridimensionata a 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identificare il tipo di file system sul disco del sistema operativo usando il `lsblk` comando con il `-f` flag:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. In base al tipo di file system, usare i comandi appropriati per ridimensionarlo.
   
   Per **xfs**, usare il comando seguente:
   
   ```
   #xfs_growfs /
   ```
   
   Output di esempio:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Per **ext4**, usare il comando seguente:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Verificare l'aumento delle dimensioni del file system per **df -Th** usando il comando seguente:
   
   ```
   #df -Thl
   ```
   
   Output di esempio:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Nell'esempio precedente è possibile osservare che le dimensioni file system per il disco del sistema operativo sono aumentate.

### <a name="rhel"></a>RHEL

Per aumentare le dimensioni del disco del sistema operativo in RHEL 7.x con LVM:

1. Arrestare la VM.
1. Aumentare le dimensioni del disco del sistema operativo dal portale.
1. Avviare la VM.

Una volta riavviata la VM, seguire questa procedura:

1. Accedere alla macchina virtuale come utente **radice** usando il comando seguente:
 
   ```
   #sudo su
   ```

1. Installare il pacchetto **gptfdisk**, necessario per aumentare le dimensioni del disco del sistema operativo.

   ```
   #yum install gdisk -y
   ```

1. Per vedere il settore più grande disponibile sul disco, eseguire il comando seguente:

   ```
   #sgdisk -e /dev/sda
   ```

1. Ridimensionare la partizione senza eliminarla usando il comando seguente. Il comando **parted** include un'opzione denominata **resizepart** per ridimensionare la partizione senza eliminarla. Il numero 4 dopo **resizepart** indica il ridimensionamento della quarta partizione.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Eseguire il comando seguente per verificare se le dimensioni della partizione sono aumentate:

   ```
   #lsblk
   ```

   L'output seguente mostra che la partizione **/dev/sda4** è stata ridimensionata a 99 GB.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Usare il comando seguente per ridimensionare il volume fisico:

   ```
   #pvresize /dev/sda4
   ```

   L'output seguente mostra che il volume fisico è stato ridimensionato a 99,02 GB.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. Nell'esempio seguente **/dev/mapper/rootvg-rootlv** viene ridimensionato da 2 GB a 12 GB (un incremento di 10 GB) tramite il comando seguente. Questo comando ridimensiona anche il file system.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Output di esempio:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Verificare se **/dev/mapper/rootvg-rootlv** include dimensioni aumentate del file system usando il comando seguente:

   ```
   #df -Th /
   ```

   Output di esempio:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Per usare la stessa procedura per ridimensionare qualsiasi altro volume logico, cambiare il nome **lv** nel passaggio 7.

## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare il disco](expand-disks.md)
