---
title: Come ridimensionare i dischi crittografati con crittografia dischi di Azure
description: Questo articolo fornisce istruzioni per il ridimensionamento dei dischi crittografati ADE usando la gestione dei volumi logici.
author: jofrance
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ddd6097fffbc02c9b7b027bcb712e20cc47f2f96
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487960"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Come ridimensionare i dispositivi di gestione dei volumi logici che usano crittografia dischi di Azure

Questo articolo illustra come ridimensionare i dischi dati che usano crittografia dischi di Azure. Per ridimensionare i dischi, si userà Logical Volume Management (LVM) in Linux. I passaggi si applicano a più scenari.

È possibile utilizzare questo processo di ridimensionamento negli ambienti seguenti:

- Distribuzioni di Linux:
    - Red Hat Enterprise Linux (RHEL) 7 o versione successiva
    - Ubuntu 16 o versione successiva
    - SUSE 12 o versione successiva
- Versioni di crittografia dischi di Azure: 
    - Estensione a passaggio singolo
    - Estensione dual-pass

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che l'utente abbia:

- Una configurazione LVM esistente. Per altre informazioni, vedere [configurare LVM in una macchina virtuale Linux](configure-lvm.md).

- Dischi già crittografati con crittografia dischi di Azure. Per altre informazioni, vedere [configurare LVM e RAID nei dispositivi crittografati](how-to-configure-lvm-raid-on-crypt.md).

- Esperienza con Linux e LVM.

- Esperienza nell'uso dei percorsi */dev/disk/SCSI1/* per i dischi dati in Azure. Per altre informazioni, vedere [risolvere i problemi relativi al nome del dispositivo VM Linux](../troubleshooting/troubleshoot-device-names-problems.md). 

## <a name="scenarios"></a>Scenari

Le procedure descritte in questo articolo si applicano agli scenari seguenti:

- Configurazioni LVM e LVM-on-crypt tradizionali
- Crittografia LVM tradizionale 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Configurazioni LVM e LVM-on-crypt tradizionali

Le configurazioni LVM e LVM-on-crypt tradizionali estendono un volume logico (LV) quando il gruppo di volumi (VG) dispone dello spazio disponibile.

### <a name="traditional-lvm-encryption"></a>Crittografia LVM tradizionale 

Nella crittografia LVM tradizionale i LVs vengono crittografati. L'intero disco non è crittografato.

Con la crittografia LVM tradizionale è possibile:

- Estendere la LV quando si aggiunge un nuovo volume fisico (PV).
- Estendere la LV quando si ridimensiona un PV esistente.

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

Il metodo consigliato per la crittografia del disco è LVM-on-Encrypt. Questo metodo esegue la crittografia dell'intero disco, non solo del LV.

Con LVM-on-crypt è possibile: 

- Estendere la LV quando si aggiunge un nuovo PV.
- Estendere la LV quando si ridimensiona un PV esistente.

> [!NOTE]
> Non è consigliabile combinare la crittografia LVM tradizionale e LVM-on-crypt nella stessa VM.

Le sezioni seguenti forniscono esempi di come usare LVM e LVM-on-crypt. Gli esempi usano valori preesistenti per dischi, PVs, VGs, LVs, file System, identificatori univoci universali (UUID) e punti di montaggio. Sostituire questi valori con i propri valori per adattarli all'ambiente in uso.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Estendere un valore LV quando lo spazio disponibile per la VG è

Il modo tradizionale per ridimensionare LVs consiste nell'estendere un valore LV quando lo spazio disponibile per la VG è. È possibile usare questo metodo per i dischi non crittografati, i volumi tradizionali con crittografia LVM e le configurazioni LVM-on-crypt.

1. Verificare le dimensioni correnti del file system che si desidera aumentare:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del file system. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verificare che il VG disponga di spazio sufficiente per aumentare la LV:

    ``` bash
    vgs
    ```

    ![Screenshot che mostra il codice che controlla lo spazio su VG. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    È anche possibile usare `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot che mostra il codice visualizzato V G che controlla lo spazio su VG. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identificare il LV da ridimensionare:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il risultato del comando l s b l k. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Per LVM-on-crypt, la differenza è che questo output mostra che il livello crittografato è a livello di disco.

    ![Screenshot che mostra il risultato del comando l s b l k. L'output è evidenziato. Mostra il livello crittografato.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Controllare le dimensioni di LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Screenshot che mostra il codice che controlla la dimensione logica del volume. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumentare le dimensioni LV usando `-r` per ridimensionare il file System online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che aumenta le dimensioni del volume logico. Il comando e i risultati sono evidenziati.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verificare le nuove dimensioni per LV e la file system:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che verifica le dimensioni di LV e file system. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    L'output delle dimensioni indica che LV e file system sono stati ridimensionati correttamente.

È possibile controllare di nuovo le informazioni di LV per confermare le modifiche a livello di LV:

``` bash
lvdisplay lvname
```

![Screenshot che mostra il codice che conferma le nuove dimensioni. Le dimensioni sono evidenziate.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Estendere un volume LVM tradizionale aggiungendo un nuovo PV

Quando è necessario aggiungere un nuovo disco per aumentare la dimensione VG, estendere il volume LVM tradizionale aggiungendo un nuovo PV.

1. Verificare le dimensioni correnti del file system che si desidera aumentare:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni correnti di un file system. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verificare la configurazione PV corrente:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che controlla la configurazione PV corrente. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Controllare le informazioni VG correnti:

    ``` bash
    vgs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni sul gruppo di volumi corrente. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Controllare l'elenco dei dischi corrente. Identificare i dischi dati controllando i dispositivi in */dev/disk/Azure/SCSI1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot che mostra il codice che controlla l'elenco dei dischi corrente. Il comando e i risultati sono evidenziati.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Controllare l'output di `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Screenshot che mostra il codice che controlla l'output di l s b l k. Il comando e i risultati sono evidenziati.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Per aggiungere il nuovo disco alla macchina virtuale, seguire le istruzioni riportate in [aggiungere un disco dati a una VM Linux](attach-disk-portal.md).

7. Controllare l'elenco dei dischi e notare il nuovo disco.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot che mostra il codice che controlla l'elenco dei dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Screenshot che mostra il codice che controlla l'elenco dei dischi utilizzando l s b l k. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Creare un nuovo PV sul nuovo disco dati:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Screenshot che mostra il codice che crea un nuovo PV. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Questo metodo usa l'intero disco come PV senza una partizione. In alternativa, è possibile utilizzare `fdisk` per creare una partizione e quindi utilizzarla per `pvcreate` .

9. Verificare che il PV sia stato aggiunto all'elenco PV:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che mostra l'elenco dei volumi fisici. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estendere il VG aggiungendo il nuovo PV:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Screenshot che mostra il codice che estende il gruppo di volumi. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Controllare le nuove dimensioni VG:

    ``` bash
    vgs
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Usare `lsblk` per identificare il LV che deve essere ridimensionato:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il codice che identifica il volume locale che deve essere ridimensionato. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Estendere la dimensione LV usando `-r` per aumentare la file System online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che aumenta le dimensioni del file system online. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verificare le nuove dimensioni di LV e file system:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del volume locale e della file system. Il comando e il risultato sono evidenziati.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Quando si usa la crittografia dei dati di Azure in configurazioni LVM tradizionali, il livello crittografato viene creato a livello di LV, non a livello di disco.
    >
    >A questo punto, il livello crittografato viene espanso nel nuovo disco. Il disco dati effettivo non dispone di impostazioni di crittografia a livello di piattaforma, quindi il relativo stato di crittografia non viene aggiornato.
    >
    >Di seguito sono riportati alcuni dei motivi per cui LVM-on-crypt è l'approccio consigliato. 

15. Controllare le informazioni di crittografia dal portale:

    ![Screenshot che mostra le informazioni di crittografia nel portale. Il nome del disco e la crittografia sono evidenziati.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Per aggiornare le impostazioni di crittografia sul disco, aggiungere un nuovo valore LV e abilitare l'estensione nella macchina virtuale.
    
16. Aggiungere un nuovo LV, creare un file system su di esso e aggiungerlo a `/etc/fstab` .

17. Impostare di nuovo l'estensione di crittografia. Questa volta le impostazioni di crittografia del nuovo disco dati verranno contrassegnate a livello di piattaforma. Ecco un esempio dell'interfaccia della riga di comando:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Controllare le informazioni di crittografia dal portale:

    ![Screenshot che mostra le informazioni di crittografia nel portale. Il nome del disco e le informazioni di crittografia sono evidenziati.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Dopo l'aggiornamento delle impostazioni di crittografia, è possibile eliminare il nuovo LV. Eliminare anche la voce da `/etc/fstab` e `/etc/crypttab` che è stata creata.

![Screenshot che mostra il codice che elimina il nuovo volume logico. Vengono evidenziate la scheda F S eliminata e la scheda Crypt.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Per completare la pulizia, attenersi alla procedura seguente:

1. Smontare il BT:

    ``` bash
    umount /mountpoint
    ```

1. Chiudere il livello crittografato del volume:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Eliminare il LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Estendere un volume LVM tradizionale ridimensionando un PV esistente

In alcuni scenari, le limitazioni potrebbero richiedere il ridimensionamento di un disco esistente. Ecco come:

1. Identificare i dischi crittografati:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot che mostra il codice che identifica i dischi crittografati. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Screenshot che illustra il codice alternativo che identifica i dischi crittografati. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Controllare le informazioni PV:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni sul volume fisico. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    I risultati nell'immagine mostrano che tutto lo spazio su tutti i PVs è attualmente in uso.

3. Controllare le informazioni sul VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Screenshot che mostra il codice che controlla le informazioni sul gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Controllare le dimensioni del disco. È possibile utilizzare `fdisk` o `lsblk` per elencare le dimensioni dell'unità.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni dei dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Qui è stato identificato quale PVs è associato a quale LVs usando `lsblk -fs` . È possibile identificare le associazioni eseguendo `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Screenshot che illustra un metodo alternativo per identificare le associazioni di volumi fisici con volumi locali. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    In questo caso, tutte e quattro le unità dati fanno parte dello stesso valore VG e di un singolo LV. La configurazione potrebbe variare.

5. Controllare l'utilizzo del file system corrente:

    ``` bash
    df -h /datalvm*
    ```

    ![Screenshot che mostra il codice che controlla file system utilizzo. Il comando e i risultati sono evidenziati.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Ridimensionare i dischi dati seguendo le istruzioni in [espandere un disco gestito di Azure](expand-disks.md#expand-an-azure-managed-disk). È possibile usare il portale, l'interfaccia della riga di comando o PowerShell.

    >[!IMPORTANT]
    >Non è possibile ridimensionare i dischi virtuali mentre la macchina virtuale è in esecuzione. Deallocare la VM per questo passaggio.

7. Avviare la macchina virtuale e controllare le nuove dimensioni usando `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del disco. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    In questo caso, `/dev/sdd` è stato ridimensionato da 5 g a 20 g.

8. Controllare le dimensioni PV correnti:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni della P V. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Anche se il disco è stato ridimensionato, il PV ha ancora le dimensioni precedenti.

9. Ridimensionare PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Screenshot che mostra il codice che ridimensiona il volume fisico. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Controllare le dimensioni PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del volume fisico. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Applicare la stessa procedura per tutti i dischi che si desidera ridimensionare.

11. Controllare le informazioni sul VG.

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per il gruppo di volumi. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    Lo spazio VG ora è sufficiente per essere allocato a LVs.

12. Ridimensionare la LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Screenshot che mostra il codice che ridimensiona L V. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Controllare le dimensioni del file system:

    ``` bash
    df -h /datalvm2
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del file system. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Estendere un volume LVM-on-crypt aggiungendo un nuovo PV

È anche possibile estendere un volume LVM-on-crypt aggiungendo un nuovo PV. Questo metodo segue attentamente i passaggi in [configurare LVM e RAID nei dispositivi crittografati](how-to-configure-lvm-raid-on-crypt.md#general-steps). Vedere le sezioni che illustrano come aggiungere un nuovo disco e configurarlo in una configurazione LVM-on-crypt.

È possibile usare questo metodo per aggiungere spazio a un valore LV esistente. In alternativa, è possibile creare nuovi VGs o LVs.

1. Verificare le dimensioni correnti del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verificare le dimensioni del file system e del LV che si desidera espandere:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che controlla la dimensione del volume locale. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni del file system. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Aggiungere un nuovo disco dati alla macchina virtuale e identificarlo.

    Prima di aggiungere il nuovo disco, controllare i dischi:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni dei dischi. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Ecco un altro modo per verificare i dischi prima di aggiungere il nuovo disco:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra un codice alternativo che controlla le dimensioni dei dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Per aggiungere il nuovo disco, è possibile usare PowerShell, l'interfaccia della riga di comando di Azure o la portale di Azure. Per altre informazioni, vedere [alleghi un disco dati a una VM Linux](attach-disk-portal.md).

    Lo schema del nome del kernel si applica al dispositivo appena aggiunto. A una nuova unità viene normalmente assegnata la lettera successiva disponibile. In questo caso, il disco aggiunto è `sdd` .

4. Controllare i dischi per assicurarsi che sia stato aggiunto il nuovo disco:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot che mostra il codice che elenca i dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il disco appena aggiunto nell'output.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Creare una file system al di sopra del disco aggiunto di recente. Associare il disco ai dispositivi collegati `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Screenshot che mostra il codice che crea un file system. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Screenshot che illustra il codice aggiuntivo che consente di creare una file system e di abbinare il disco ai dispositivi collegati. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Creare un punto di montaggio temporaneo per il nuovo disco aggiunto:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Aggiungere la file system creata di recente a `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Montare il file system appena creato:

    ``` bash
    mount -a
    ```

9. Verificare che il nuovo file system sia montato:

    ``` bash
    df -h
    ```

    ![Screenshot che mostra il codice che verifica che il file system sia montato. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Screenshot che Mostra codice aggiuntivo che verifica che il file system sia montato. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Riavviare la crittografia avviata in precedenza per le unità dati.

    >[!TIP]
    >Per LVM-on-crypt, è consigliabile usare `EncryptFormatAll` . In caso contrario, è possibile che venga visualizzata una doppia crittografia quando si impostano dischi aggiuntivi.
    >
    >Per altre informazioni, vedere [configurare LVM e RAID nei dispositivi crittografati](how-to-configure-lvm-raid-on-crypt.md).

    Di seguito è riportato un esempio:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Al termine della crittografia, viene visualizzato un livello Crypt nel disco appena aggiunto:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il codice che controlla il livello di crittografia. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Smontare il livello crittografato del nuovo disco:

    ``` bash
    umount ${newmount}
    ```

12. Controllare le informazioni PV correnti:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni sul volume fisico. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Creare una PV sul livello crittografato del disco. Prendere il nome del dispositivo dal `lsblk` comando precedente. Aggiungere un `/dev/` Mapper davanti al nome del dispositivo per creare il PV:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Screenshot che mostra il codice che crea un volume fisico sul livello crittografato. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Viene visualizzato un avviso relativo alla cancellazione della `ext4 fs` firma corrente. Si tratta di un comportamento previsto. Rispondere a questa domanda con `y` .

14. Verificare che il nuovo PV sia stato aggiunto alla configurazione LVM:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che verifica che il volume fisico è stato aggiunto alla configurazione LVM. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Aggiungere il nuovo PV al VG che è necessario aumentare.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Screenshot che mostra il codice che aggiunge un volume fisico a un gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verificare le nuove dimensioni e lo spazio disponibile del VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot che mostra il codice che verifica le dimensioni e lo spazio libero del gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Si noti l'incremento del `Total PE` conteggio e di `Free PE / Size` .

17. Aumentare le dimensioni di LV e file system. Usare l' `-r` opzione in `lvextend` . In questo esempio viene aggiunto lo spazio totale disponibile in VG alla LV specificata.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che aumenta le dimensioni del volume locale e della file system. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Seguire i passaggi successivi per verificare le modifiche.

1. Verificare le dimensioni del LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che verifica la nuova dimensione del volume locale. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Verificare la nuova dimensione del file system:

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot che mostra il codice che verifica le nuove dimensioni del file system. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Verificare che il livello LVM si trovi sopra il livello crittografato:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il codice che verifica che il livello LVM si trovi sopra il livello crittografato. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Se si usano `lsblk` senza opzioni, i punti di montaggio vengono visualizzati più volte. Il comando Ordina per dispositivo e LVs. 

    Potrebbe essere necessario utilizzare `lsblk -fs` . In questo comando `-fs` inverte l'ordinamento in modo che i punti di montaggio siano visualizzati una sola volta. I dischi vengono visualizzati più volte.

    ``` bash
    lsblk -fs
    ```

    ![Screenshot che illustra il codice alternativo che verifica che il livello LVM si trovi sopra il livello crittografato. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Estendere un LVM in un volume di crittografia ridimensionando un PV esistente

1. Identificare i dischi crittografati:

    ``` bash
    lsblk
    ```

    ![Screenshot che mostra il codice che identifica i dischi crittografati. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Screenshot che illustra il codice alternativo che identifica i dischi crittografati. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Controllare le informazioni PV:

    ``` bash
    pvs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per i volumi fisici. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Controllare le informazioni sul VG:

    ``` bash
    vgs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per i gruppi di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Controllare le informazioni LV:

    ``` bash
    lvs
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per il volume locale. Il risultato è evidenziato.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Controllare l'utilizzo del file system:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Screenshot che mostra il codice che controlla la quantità di file system in uso. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Controllare le dimensioni dei dischi:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni dei dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Ridimensionare il disco dati. È possibile usare il portale, l'interfaccia della riga di comando o PowerShell. Per altre informazioni, vedere la sezione disk-Resize in [espandere dischi rigidi virtuali in una macchina virtuale Linux](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Non è possibile ridimensionare i dischi virtuali mentre la macchina virtuale è in esecuzione. Deallocare la VM per questo passaggio.

8. Controllare le dimensioni dei dischi:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot che mostra il codice che controlla le dimensioni dei dischi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    In questo caso, entrambi i dischi sono stati ridimensionati da 2 a 4 GB. Tuttavia, le dimensioni di file system, LV e PV rimangono invariate.

9. Controllare le dimensioni PV correnti. Tenere presente che in LVM-on-crypt, PV è il `/dev/mapper/` dispositivo, non il `/dev/sd*` dispositivo.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot che mostra il codice che controlla la dimensione del volume fisico corrente. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Ridimensionare PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Screenshot che mostra il codice che ridimensiona il volume fisico. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Controllare le nuove dimensioni PV:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot che mostra il codice che controlla la dimensione del volume fisico. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Ridimensionare il livello crittografato nel PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Applicare la stessa procedura per tutti i dischi che si desidera ridimensionare.

13. Controllare le informazioni sul VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per il gruppo di volumi. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    Lo spazio VG ora è sufficiente per essere allocato a LVs.

14. Controllare le informazioni LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot che mostra il codice che controlla le informazioni per il volume locale. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Controllare l'utilizzo del file system:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che controlla l'utilizzo del file system. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Ridimensionare la LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot che mostra il codice che ridimensiona il volume locale. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Qui viene usata l' `-r` opzione per ridimensionare anche il file System.

17. Controllare le informazioni LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot che mostra il codice che ottiene informazioni sul volume locale. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Controllare l'utilizzo del file system:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot che mostra il codice che controlla l'utilizzo del file system. I risultati sono evidenziati.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Applicare la stessa procedura di ridimensionamento a qualsiasi altro LV che lo richieda.

## <a name="next-steps"></a>Passaggi successivi

[Risolvere i problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
