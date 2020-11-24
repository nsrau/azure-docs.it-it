---
title: Ridimensionare un disco del sistema operativo con una partizione GPT
description: Questo articolo fornisce istruzioni su come ridimensionare un disco del sistema operativo con una partizione GPT (GUID Partition Table) in Linux.
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
ms.openlocfilehash: 3565b165c669af3566667d9bdfa401d15fcce101
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544157"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ridimensionare un disco del sistema operativo con una partizione GPT

> [!NOTE]
> Questo articolo si applica solo ai dischi del sistema operativo che dispongono di una partizione GPT (tabella di partizione GUID).

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

Per aumentare le dimensioni del disco del sistema operativo in Ubuntu 16. *x* e 18. *x*:

1. Arrestare la VM.
1. Aumentare le dimensioni del disco del sistema operativo dal portale.
1. Riavviare la macchina virtuale e quindi accedere alla macchina virtuale come utente **root** .
1. Verificare che il disco del sistema operativo ora visualizzi dimensioni del file system aumentate.

Nell'esempio seguente il disco del sistema operativo è stato ridimensionato dal portale a 100 GB. Il file system **/dev/sda1** montato su **/** ora visualizza 97 GB.

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

Al riavvio della macchina virtuale, completare i passaggi seguenti:

1. Accedere alla macchina virtuale come utente **root** usando questo comando:

   ```
   # sudo -i
   ```

1. Usare il comando seguente per installare il pacchetto **growpart** , che verrà usato per ridimensionare la partizione:

   ```
   # zypper install growpart
   ```

1. Usare il `lsblk` comando per trovare la partizione montata nella radice del file System ( **/** ). In questo caso, si noterà che la partizione 4 del dispositivo **SDA** è montata in **/** :

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

1. Ridimensionare la partizione necessaria usando il `growpart` comando e il numero di partizione determinato nel passaggio precedente:

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
   
   Per **XFS**, usare questo comando:
   
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
   
   Per **ext4** usare questo comando:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Verificare l'aumento delle dimensioni di file system per **DF-TH** utilizzando questo comando:
   
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

### <a name="rhel-with-lvm"></a>RHEL con LVM

1. Accedere alla macchina virtuale come utente **root** usando questo comando:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Usare il `lsblk` comando per determinare il volume logico (LV) montato nella radice del file System ( **/** ). In questo caso, si noterà che **rootvg-rootlv** è montato su **/** . Se si vuole un altro file system, sostituire il punto di montaggio e la LV in questo articolo.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Controllare se è presente spazio libero nel gruppo di volumi LVM (VG) che contiene la partizione radice. Se lo spazio è disponibile, andare al passaggio 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   In questo esempio, la linea **PE/dimensioni libere** indica che sono disponibili 38,02 GB di spazio libero nel gruppo di volumi. Non è necessario ridimensionare il disco prima di aggiungere spazio al gruppo di volumi.

1. Per aumentare le dimensioni del disco del sistema operativo in RHEL 7. *x* con LVM:

   1. Arrestare la VM.
   1. Aumentare le dimensioni del disco del sistema operativo dal portale.
   1. Avviare la VM.

1. Quando la macchina virtuale è stata riavviata, installare il pacchetto **cloud-utils-growpart** per ottenere il `growpart` comando, che è necessario per aumentare le dimensioni del disco del sistema operativo.

      Questo pacchetto è preinstallato nella maggior parte delle immagini di Azure Marketplace.

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. Determinare il disco e la partizione che contengono il volume fisico o i volumi LVM (PV) nel gruppo di volumi denominato **rootvg** usando il `pvscan` comando. Prendere nota delle dimensioni e dello spazio libero elencati tra parentesi quadre (**[** e **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Verificare le dimensioni della partizione utilizzando `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Espandere la partizione che contiene questo PV usando `growpart` , il nome del dispositivo e il numero di partizione. In questo modo, la partizione specificata sarà espansa per usare tutto lo spazio libero contiguo sul dispositivo.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Verificare che la partizione sia stata ridimensionata in base alle dimensioni previste utilizzando di `lsblk` nuovo il comando. Si noti che nell'esempio **sda4** è stato modificato da 63 gb a 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Espandere il PV per usare il resto della partizione appena espansa:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Verificare che le nuove dimensioni del PV siano le dimensioni previste, confrontandolo con i valori originali **[size/Free]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Espandere il volume logico desiderato (LV) in base all'importo desiderato. Non è necessario che lo spazio sia disponibile nel gruppo di volumi. Nell'esempio seguente **/dev/mapper/rootvg-rootlv** viene ridimensionato da 2 GB a 12 GB (un aumento di 10 GB). Questo comando ridimensiona anche il file system.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Output di esempio:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. Il `lvresize` comando chiama automaticamente il comando di ridimensionamento appropriato per il file System in LV. Controllare se **/dev/mapper/rootvg-rootlv**, montato in **/** , ha un aumento delle dimensioni di file System usando questo comando:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Output di esempio:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Per usare la stessa procedura per ridimensionare qualsiasi altro volume logico, modificare il nome LV nel passaggio 12.

### <a name="rhel-raw"></a>RAW RHEL
>[!NOTE]
>Eseguire sempre uno snapshot della macchina virtuale prima di aumentare le dimensioni del disco del sistema operativo.

Per aumentare le dimensioni del disco del sistema operativo in una partizione RAW RHEL:

1. Arrestare la VM.
1. Aumentare le dimensioni del disco del sistema operativo dal portale.
1. Avviare la VM.

Al riavvio della macchina virtuale, completare i passaggi seguenti:

1. Accedere alla macchina virtuale come utente **root** usando questo comando:
 
   ```
   sudo su
   ```

1. Installare il pacchetto **gptfdisk** , che è necessario per aumentare le dimensioni del disco del sistema operativo:

   ```
   yum install gdisk -y
   ```

1.  Per visualizzare tutti i settori disponibili sul disco, eseguire questo comando:
    ```
    gdisk -l /dev/sda
    ```

1. Verranno visualizzati i dettagli che informano il tipo di partizione. Assicurarsi che sia GPT. Identificare la partizione radice. Non modificare o eliminare la partizione di avvio (partizione di avvio BIOS) o la partizione di sistema (partizione di sistema EFI).

1. Usare questo comando per avviare il partizionamento per la prima volta: 
    ```
    gdisk /dev/sda
    ```

1. Verrà visualizzato un messaggio che richiede il comando successivo: `Command: ? for help` . Selezionare il tasto **w** :

   ```
   w
   ```

1. Si riceverà questo messaggio: `Warning! Secondary header is placed too early on the disk! Do you want to
correct this problem? (Y/N)` . Selezionare il tasto **Y** : 

   ```
   Y
   ```

1. Verrà visualizzato un messaggio che informa che i controlli finali sono completati e viene richiesta la conferma. Selezionare il tasto **Y** :

   ```
   Y
   ```

1. Usare il `partprobe` comando per verificare se tutto è stato eseguito correttamente:

   ```
   partprobe
   ```

1. Sono stati completati i passaggi precedenti per assicurarsi che l'intestazione GPT secondaria venga posizionata alla fine. Successivamente, avviare il processo di ridimensionamento utilizzando di `gdisk` nuovo lo strumento. Usare il comando seguente:

   ```
   gdisk /dev/sda
   ```
1. Nel menu dei comandi selezionare il tasto **p** per visualizzare un elenco di partizioni. Identificare la partizione radice. In questi passaggi, **sda2** viene considerato la partizione radice. Identificare la partizione di avvio. (In questi passaggi, **sda3** è considerato la partizione di avvio). 

   ```
   p
   ```
    ![Screenshot che mostra la partizione radice e la partizione di avvio.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Selezionare il tasto **d** per eliminare la partizione. Quindi selezionare il numero di partizione assegnato alla partizione di avvio. (In questo esempio, è **3**).
   ```
   d
   3
   ```
1. Selezionare il tasto **d** per eliminare la partizione. Selezionare il numero di partizione assegnato alla partizione di avvio. (In questo esempio, è **2**).
   ```
   d
   2
   ```
    ![Screenshot che illustra i passaggi per eliminare le partizioni radice e di avvio.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Per ricreare la partizione radice con dimensioni maggiori, selezionare il tasto **n** , quindi immettere il numero di partizione eliminato in precedenza per la radice (**2** in questo esempio). Scegliere `Default Value` per il primo settore. Scegliere `Last sector value -  boot size sector` per l'ultimo settore ( `4096` in questo caso, corrispondente a 2 MB di avvio). Scegliere `8300` per il codice esadecimale.
   ```
   n
   2
   (Enter default)
   (Calculated value of Last sector value - 4096)
   8300
   ```
1. Per ricreare la partizione di avvio, selezionare il tasto **n** , quindi immettere il numero di partizione eliminato in precedenza per l'avvio (**3** in questo esempio). Scegliere `Default Value` per il primo settore e l'ultimo settore. Scegliere `EF02` per il codice esadecimale.
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Scrivere le modifiche usando il `w` comando e quindi selezionare `Y` per confermare le modifiche:
   ```
   w
   Y
   ```
1. Eseguire il `partprobe` comando per verificare la stabilità del disco:
   ```
   partprobe
   ```
1. Riavviare la macchina virtuale. Le dimensioni della partizione radice devono essere aumentate.
   ```
   reboot
   ```

   ![Screenshot che illustra i passaggi per ricreare la partizione di avvio.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Eseguire il `xfs_growfs` comando nella partizione per ridimensionarlo:
   ```
   xfs_growfs /dev/sda2
   ```

   ![Screenshot che mostra il risultato dell'esecuzione di xfs_growfs.](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare il disco](expand-disks.md)
