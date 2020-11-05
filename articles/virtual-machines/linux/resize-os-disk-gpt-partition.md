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
ms.openlocfilehash: 99b723322ce7636edce3ae5b59a69b96e288ca24
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392691"
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
   
   Per **xfs** , usare il comando seguente:
   
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
   
   Per **ext4** , usare il comando seguente:
   
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

### <a name="rhel-with-lvm"></a>RHEL con LVM

1. Accedere alla macchina virtuale come utente **radice** usando il comando seguente:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Usare il `lsblk` comando per trovare il volume logico (LV) montato nella radice del file System ("/"). In questo caso, si noterà che **_rootvg-rootlv_*_ è montato su _* /**.  Se si desidera un altro file System, sostituire il punto LV e il punto di montaggio tramite questo documento.

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

1. Controllare se è presente spazio libero nel gruppo di volumi LVM contenente la partizione radice.  Se lo spazio è disponibile, andare al passaggio **12**

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

   In questo esempio, la riga di **PE/dimensioni libere** indica che 38.02 GB è disponibile nel gruppo di volumi.  Non è necessario alcun ridimensionamento del disco prima di aggiungere spazio al gruppo di volumi

1. Per aumentare le dimensioni del disco del sistema operativo in RHEL 7.x con LVM:

   1. Arrestare la VM.
   1. Aumentare le dimensioni del disco del sistema operativo dal portale.
   1. Avviare la VM.

1. Una volta riavviata la VM, seguire questa procedura:

   1. Installare il pacchetto **cloud-utils-growpart** per specificare il comando **growpart** , che è necessario per aumentare le dimensioni del disco del sistema operativo.

      Questo pacchetto è preinstallato nella maggior parte delle immagini di Azure Marketplace.

      ```bash
      [root@dd-rhel7vm ~]# yum install cloud-utils-growpart
      ```

1. Determinare quale disco e partizione contenga i volumi fisici LVM (PV) nel gruppo di volumi (VG) denominato rootvg con il comando **pvscan** .  Prendere nota delle dimensioni e dello spazio libero elencati tra parentesi quadre **[]**.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Verificare le dimensioni della partizione con **lsblk**.  Esaminare il 

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

1. Espandere la partizione che contiene questo PV usando **growpart** , il nome del dispositivo e il numero di partizione.  La partizione specificata verrà espansa in modo da usare tutto lo spazio contiguo disponibile nel dispositivo.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Verificare che la partizione sia stata ridimensionata con le dimensioni previste con il comando **lsblk** .  Si noti che nell'esempio sda4 è stato modificato da 63G a 95G.

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

1. Espandere il PV per usare il resto della partizione appena espansa

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Verificare che le nuove dimensioni del PV siano le dimensioni previste, confrontando i valori originali **[size/Free]** .

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Espandere il volume logico desiderato (LV) in base alla quantità desiderata, che non deve necessariamente corrispondere a tutto lo spazio disponibile nel gruppo di volumi.  Nell'esempio seguente **/dev/mapper/rootvg-rootlv** viene ridimensionato da 2 GB a 12 GB (un incremento di 10 GB) tramite il comando seguente. Questo comando ridimensiona anche il file system.

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

1. Il comando lvresize chiama automaticamente il comando di ridimensionamento appropriato per il file System in LV. Verificare che **/dev/mapper/rootvg-rootlv** , montato su, **/** abbia un aumento delle dimensioni di file System usando il comando seguente:

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
> Per usare la stessa procedura per ridimensionare qualsiasi altro volume logico, modificare il nome **LV** nel passaggio **12**.

### <a name="rhel-raw"></a>RAW RHEL
>[!NOTE]
>Eseguire sempre uno snapshot della macchina virtuale prima di aumentare le dimensioni del disco del sistema operativo.

Per aumentare le dimensioni del disco del sistema operativo in RHEL con una partizione non ELABORAta:

Arrestare la VM.
Aumentare le dimensioni del disco del sistema operativo dal portale.
Avviare la VM.
Una volta riavviata la VM, seguire questa procedura:

1. Accedere alla macchina virtuale come utente **radice** usando il comando seguente:
 
   ```
   sudo su
   ```

1. Installare il pacchetto **gptfdisk** , necessario per aumentare le dimensioni del disco del sistema operativo.

   ```
   yum install gdisk -y
   ```

1.  Per visualizzare tutti i settori disponibili sul disco, eseguire il comando seguente:
    ```
    gdisk -l /dev/sda
    ```

1. Vengono visualizzati i dettagli che informano il tipo di partizione. Verificare che sia GPT. Identificare la partizione radice. Non modificare o eliminare la partizione di avvio (partizione di avvio BIOS) e la partizione di sistema ("partizione di sistema EFI")

1. Usare il comando seguente per avviare il partizionamento per la prima volta. 
    ```
    gdisk /dev/sda
    ```

1. A questo punto verrà visualizzato un messaggio che richiede il comando successivo (' Command:? per la Guida di. 

   ```
   w
   ```

1. Verrà visualizzato un messaggio di avviso che informa che L'intestazione secondaria è posizionata troppo presto sul disco. Risolvere il problema? (S/N): ". È necessario premere ' Y '

   ```
   Y
   ```

1. Verrà visualizzato un messaggio che informa che i controlli finali sono stati completati e viene richiesta una conferma. Premere ' Y '

   ```
   Y
   ```

1. Controllare se il problema si è verificato correttamente usando il comando partprobe

   ```
   partprobe
   ```

1. I passaggi precedenti hanno verificato che l'intestazione GPT secondaria venga posizionata alla fine. Il passaggio successivo consiste nell'avviare il processo di ridimensionamento utilizzando di nuovo lo strumento gdisk. Usare il comando seguente.

   ```
   gdisk /dev/sda
   ```
1. Nel menu dei comandi premere "p" per visualizzare l'elenco di partizioni. Identificare la partizione radice (nei passaggi, sda2 è considerato la partizione radice) e la partizione di avvio (nei passaggi, sda3 viene considerata come partizione di avvio) 

   ```
   p
   ```
    ![Partizione radice e partizione di avvio](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Premere ' d'per eliminare la partizione e selezionare il numero di partizione assegnato all'avvio (in questo esempio è' 3')
   ```
   d
   3
   ```
1. Premere ' d'per eliminare la partizione e selezionare il numero di partizione assegnato all'avvio (in questo esempio è' 2')
   ```
   d
   2
   ```
    ![Elimina partizione radice e partizione di avvio](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Per ricreare la partizione radice con dimensioni maggiori, premere ' n', immettere il numero di partizione eliminato in precedenza per la radice (' 2' per questo esempio) e scegliere il primo settore come ' valore predefinito ', ultimo settore come ' ultimo valore settore-dimensioni avvio settore ' (' 4096 in questo caso ' corrispondente a 2MB di avvio) e codice esadecimale come ' 8300'
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Per ricreare la partizione di avvio, premere ' n', immettere il numero di partizione eliminato in precedenza per l'avvio (' 3' per questo esempio) e scegliere il primo settore come ' valore predefinito ', ultimo settore come ' valore predefinito ' e codice esadecimale come ' EF02'
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Scrivere le modifiche con il comando ' w ' e premere ' Y ' per confermare
   ```
   w
   Y
   ```
1. Eseguire il comando ' partprobe ' per verificare la stabilità del disco
   ```
   partprobe
   ```
1. Riavviare la macchina virtuale e le dimensioni della partizione radice aumentano
   ```
   reboot
   ```

   ![Nuova partizione radice e partizione di avvio](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Eseguire il comando xfs_growfs sulla partizione per ridimensionarlo
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS Grow FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare il disco](expand-disks.md)
