---
title: Ambiente chroot in una macchina virtuale di ripristino Linux.
description: Questo articolo descrive come risolvere i problemi dell'ambiente chroot nella macchina virtuale di ripristino (VM) in Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: d6605aa77cfa746f60fc3b23e120267a2614f7b1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82864682"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Ambiente chroot in una macchina virtuale di ripristino Linux

Questo articolo descrive come risolvere i problemi dell'ambiente chroot nella macchina virtuale di ripristino (VM) in Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount /dev/sdc1 /rescue
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x con partizioni non ELABORAte

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x con LVM

   > [!NOTE]
   > Se la macchina virtuale originale include Logical Volume Manager (LVM) nel disco del sistema operativo, creare la VM di ripristino usando l'immagine con partizioni non elaborate nel disco del sistema operativo.

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare il comando seguente per attivare il gruppo di volumi logici:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Usare il `lsblk` comando per recuperare i nomi LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/mapper/rootvg-optlv /rescue/opt
      #mount /dev/sdc2 /rescue/boot/
      #mount /dev/sdc1 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue/opt
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x con LVM

   > [!NOTE]
   > Se la macchina virtuale originale include Logical Volume Manager (LVM) nel disco del sistema operativo, creare la VM di ripristino usando l'immagine con partizioni non elaborate nel disco del sistema operativo.

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare il comando seguente per attivare il gruppo di volumi logici:

      ```
      #vgscan --mknodes
      #vgchange -ay
      #lvscan
      ```

   1. Usare il `lsblk` comando per recuperare i nomi LVM:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount /dev/mapper/rootvg-rootlv /rescue
      #mount /dev/mapper/rootvg-varlv /rescue/var
      #mount /dev/mapper/rootvg-homelv /rescue/home
      #mount /dev/mapper/rootvg-usrlv /rescue/usr
      #mount /dev/mapper/rootvg-tmplv /rescue/tmp
      #mount /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue/home
      #umount /rescue/var
      #umount /rescue/usr
      #umount /rescue/tmp
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="oracle-7x"></a>Oracle 7. x

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc2 /rescue
      #mount -o nouuid /dev/sdc1 /rescue/boot/
      #mount /dev/sdc15 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      ##chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 per SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 per SAP

1. Arrestare o deallocare la VM interessata.
1. Creare un'immagine di macchina virtuale di ripristino della stessa versione del sistema operativo, nello stesso gruppo di risorse (RSG) e nel percorso usando un disco gestito.
1. Usare il portale di Azure per creare uno snapshot del disco del sistema operativo della macchina virtuale interessata.
1. Creare un disco dallo snapshot del disco del sistema operativo e collegarlo alla macchina virtuale di ripristino.
1. Una volta creato il disco, risolvere i problemi relativi all'ambiente chroot nella macchina virtuale di ripristino.

   1. Accedere alla macchina virtuale come utente root usando il comando seguente:

      `#sudo su -`

   1. Trovare il disco usando `dmesg` (il metodo usato per individuare il nuovo disco potrebbe variare). Nell'esempio seguente viene usato **dmesg** per filtrare i dischi **SCSI** :

      `dmesg | grep SCSI`

      L'output sarà simile all'esempio seguente. In questo esempio si vuole il disco **DSC** :

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Usare i comandi seguenti per accedere all'ambiente chroot:

      ```
      #mkdir /rescue
      #mount -o nouuid /dev/sdc4 /rescue
      #mount -o nouuid /dev/sdc3 /rescue/boot/
      #mount /dev/sdc2 /rescue/boot/efi
      #cd /rescue

      #mount -t proc proc proc
      #mount -t sysfs sys sys/
      #mount -o bind /dev dev/
      #mount -o bind /dev/pts dev/pts/
      #mount -o bind /run run/
      #chroot /rescue
      ```

   1. Risolvere i problemi relativi all'ambiente chroot.

   1. Usare i comandi seguenti per uscire dall'ambiente chroot:

      ```
      #exit

      #umount /rescue/proc/
      #umount /rescue/sys/
      #umount /rescue/dev/pts
      #umount /rescue/dev/
      #umount /rescue/run
      #cd /
      #umount /rescue/boot/efi
      #umount /rescue/boot
      #umount /rescue
      ```

      > [!NOTE]
      > Se viene visualizzato l'errore `unable to unmount /rescue` , aggiungere l'opzione-l al comando umount.
      >
      > Esempio: `umount -l /rescue`

1. Scollegare il disco dalla macchina virtuale di ripristino ed eseguire uno scambio del disco con la VM originale.
1. Avviare la macchina virtuale originale e verificare la relativa connettività.

## <a name="next-steps"></a>Passaggi successivi

- [Risolvere i problemi di connessione SSH](troubleshoot-ssh-connection.md)