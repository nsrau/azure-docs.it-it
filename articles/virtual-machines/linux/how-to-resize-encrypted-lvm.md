---
title: Come ridimensionare i dischi di gestione dei volumi logici crittografati con crittografia dischi di Azure
description: Questo articolo fornisce istruzioni per il ridimensionamento dei dischi crittografati ADE con la gestione dei volumi logici
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343967"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Come ridimensionare i dispositivi logici di gestione dei volumi crittografati con crittografia dischi di Azure

Questo articolo è un processo dettagliato su come ridimensionare i dischi dati crittografati ADE usando la gestione del volume logico (LVM) in Linux, applicabile a più scenari.

Il processo si applica agli ambienti seguenti:

- Distribuzioni di Linux
    - RHEL 7+
    - Ubuntu 16 +
    - SUSE 12 +
- Estensione a singolo passaggio di crittografia dischi di Azure
- Estensione dual-pass di crittografia dischi di Azure

## <a name="considerations"></a>Considerazioni

In questo documento si presuppone che:

1. Esiste una configurazione LVM esistente.
   
   Per altre informazioni sulla configurazione di LVM in una VM Linux, vedere [configurare LVM in una VM Linux](configure-lvm.md) .

2. I dischi sono già crittografati con crittografia dischi di Azure. per informazioni su come configurare LVM-on-crypt, vedere [configurare LVM in Crypt](how-to-configure-lvm-raid-on-crypt.md) .

3. Sono disponibili le competenze necessarie per Linux e LVM per seguire questi esempi.

4. Si comprende che la raccomandazione di usare i dischi dati in Azure, come indicato nella [sezione risoluzione dei problemi relativi ai nomi dei dispositivi](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), consiste nell'usare i percorsi/dev/disk/SCSI1/.

## <a name="scenarios"></a>Scenari

Le procedure descritte in questo articolo si applicano agli scenari seguenti:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Per le configurazioni LVM e LVM-on-crypt tradizionali

- Estensione di un volume logico quando è disponibile spazio nella VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Per la crittografia LVM tradizionale (i volumi logici vengono crittografati e non l'intero disco)

- Estensione di un volume LVM tradizionale aggiunta di un nuovo PV
- Estensione di un volume LVM tradizionale ridimensionamento di un PV esistente

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Per LVM-on-crypt (metodo consigliato, l'intero disco è crittografato, non solo il volume logico)

- Estensione di un LVM nel volume Crypt aggiunta di un nuovo PV
- Estensione di un LVM nel volume Crypt ridimensionamento di un PV esistente

> [!NOTE]
> Non è consigliabile combinare la crittografia LVM tradizionale e LVM-on-crypt nella stessa VM.

> [!NOTE]
> In questi esempi vengono utilizzati i nomi preesistenti per dischi, volumi fisici, gruppi di volumi, volumi logici, file System, UUID e montaggio, è necessario sostituire i valori forniti in questi esempi per adattarli all'ambiente in uso.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Estensione di un volume logico quando è disponibile spazio nella VG

Metodo tradizionale usato per ridimensionare i volumi logici, può essere applicato a dischi non crittografati, a volumi crittografati LVM tradizionali e a configurazioni LVM-on-crypt.

1. Verificare le dimensioni correnti del file System che si desidera aumentare:

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verificare che il VG disponga di spazio sufficiente per aumentare la LV

    ``` bash
    vgs
    ```

    ![scenarioa-check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    È anche possibile usare "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identificare il volume logico che deve essere ridimensionato

    ``` bash
    lsblk
    ```

    ![scenarioa-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Per LVM-on-crypt, la differenza riguarda questo output, che mostra che il livello crittografato si trova sul livello crittografato che copre l'intero disco

    ![scenarioa-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Controllare le dimensioni del volume logico

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumentare le dimensioni LV usando "-r" per il ridimensionamento online del file System

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-Resize-LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verificare le nuove dimensioni per LV e il file system

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    La nuova dimensione viene riflessa e indica che il ridimensionamento di LV e filesystem è riuscito

7. È possibile controllare di nuovo le informazioni di LV per confermare le modifiche a livello di LV

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Estensione di un volume LVM tradizionale aggiunta di un nuovo PV

Applicabile quando è necessario aggiungere un nuovo disco per aumentare le dimensioni del gruppo di volumi.

1. Verificare le dimensioni correnti del file System che si desidera aumentare:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verificare la configurazione del volume fisico corrente

    ``` bash
    pvs
    ```

    ![scenariob-check-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Controllare le informazioni VG correnti

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Controllare l'elenco dei dischi corrente

    I dischi dati devono essere identificati controllando i dispositivi in/dev/disk/Azure/SCSI1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-SCS1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Controllare l'output di lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Connetti il nuovo disco alla macchina virtuale

    Completare il passaggio 4 del documento seguente

   - [Aggiungere un disco a una macchina virtuale](attach-disk-portal.md)

7. Dopo aver collegato il disco, controllare l'elenco dei dischi, notare il nuovo disco

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Creare un nuovo PV sul nuovo disco dati

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Questo metodo usa l'intero disco come PV senza una partizione. Facoltativamente, è possibile usare "fdisk" per creare una partizione e quindi usare tale partizione per "pvcreate".

9. Verificare che il PV sia stato aggiunto all'elenco PV.

    ``` bash
    pvs
    ```

    ![scenariob-check-PVS1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estendere il VG aggiungendo il nuovo PV

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG-Extend](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Controllare le nuove dimensioni VG

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Usare lsblk per identificare quale LV deve essere ridimensionato

    ``` bash
    lsblk
    ```

    ![scenariob-check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Estendere la dimensione LV usando "-r" per eseguire un aumento in linea del file System

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verificare le nuove dimensioni di BT e filesystem

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    È importante tenere presente che quando ADE viene usato nelle configurazioni LVM tradizionali, il livello crittografato viene creato a livello di LV, non a livello di disco.

    A questo punto, il livello crittografato viene espanso nel nuovo disco.
    Il disco dati effettivo non dispone di alcuna impostazione di crittografia a livello di piattaforma, quindi il relativo stato di crittografia non viene aggiornato.

    >[!NOTE]
    >Di seguito sono riportati alcuni dei motivi per cui LVM-on-crypt è l'approccio consigliato. 

15. Controllare le informazioni di crittografia dal portale:

    ![scenariob-check-Portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    È necessario aggiungere un nuovo valore LV e abilitare l'estensione nella macchina virtuale per aggiornare le impostazioni di crittografia sul disco.
    
16. Aggiungere un nuovo LV, creare un file System e aggiungerlo a/etc/fstab

17. Impostare di nuovo l'estensione di crittografia per contrassegnare le impostazioni di crittografia nel nuovo disco dati a livello di piattaforma.

    Esempio:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Controllare le informazioni di crittografia dal portale:

    ![scenariob-check-Portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Al termine dell'aggiornamento delle impostazioni di crittografia, è possibile eliminare il nuovo LV. è anche necessario eliminare la voce da/etc/fstab e/etc/crypttab che sono stati creati.

    ![scenariob-Delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Smontare il volume logico

    ``` bash
    umount /mountpoint
    ```

21. Chiudere il livello crittografato del volume

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Elimina LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Estensione di un volume LVM tradizionale ridimensionamento di un PV esistente

Per determinati scenari o limitazioni è necessario ridimensionare un disco esistente.

1. Identificare i dischi crittografati

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-check-SCSI1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Controllare le informazioni PV

    ``` bash
    pvs
    ```

    ![scenarioc-check-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Tutto lo spazio su tutti i PVs è attualmente in uso

3. Controllare le informazioni di VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Controllare le dimensioni del disco, è possibile usare fdisk o lsblk per elencare le dimensioni dell'unità

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Sono stati identificati i PVs a cui è associato LVs usando lsblk-FS, che è possibile identificare anche eseguendo "lvdisplay"

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    In questo caso specifico tutte e quattro le unità dati fanno parte dello stesso valore VG e di un singolo LV, la configurazione potrebbe essere diversa da questo esempio.

5. Controllare l'utilizzo del file system corrente:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Ridimensionare i dischi dati:

    È possibile fare riferimento a [Linux Expand disks](expand-disks.md) (solo per fare riferimento al ridimensionamento del disco). per eseguire questo passaggio, è possibile usare il portale, l'interfaccia della riga di comando o PowerShell.

    >[!NOTE]
    >Tenere presente che le operazioni di ridimensionamento sui dischi virtuali non possono essere eseguite con la macchina virtuale in esecuzione. Per questo passaggio sarà necessario deallocare la macchina virtuale

7. Quando i dischi vengono ridimensionati in base al valore necessario, avviare la macchina virtuale e controllare le nuove dimensioni con fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    In questo caso particolare/dev/sdd è stato ridimensionato da 5G a 20G

8. Controllare le dimensioni PV correnti

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Anche se il disco è stato ridimensionato, il PV ha ancora le dimensioni precedenti.

9. Ridimensionare PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-check-pvResize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Controllare le dimensioni PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Applicare la stessa procedura per tutti i dischi che si desidera ridimensionare.

11. Controllare le informazioni sul VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    Il VG dispone ora di spazio da allocare a LVs

12. Ridimensionare il LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Controllare le dimensioni di FS

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-check-DF3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Estensione di un volume LVM-on-crypt aggiunta di un nuovo PV

Questo metodo segue attentamente i passaggi da [configurare LVM in Crypt](how-to-configure-lvm-raid-on-crypt.md) per aggiungere un nuovo disco e configurarlo in una configurazione LVM-on-crypt.

È possibile usare questo metodo per aggiungere spazio a un valore LV già esistente oppure è possibile creare un nuovo VGs o LVs.

1. Verificare le dimensioni correnti del VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenario-check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verificare le dimensioni del FS e del LV che si desidera aumentare

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario-check-LV01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scenario-check-FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Aggiungere un nuovo disco dati alla macchina virtuale e identificarlo.

    Controllare i dischi prima di aggiungere il disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Controllare i dischi prima di aggiungere il nuovo disco

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Aggiungere un nuovo disco con PowerShell, l'interfaccia della riga di comando di Azure o il portale di Azure. Vedere come aggiungere [un disco](attach-disk-portal.md) per informazioni di riferimento sull'aggiunta di dischi a una macchina virtuale.

    In seguito allo schema del nome del kernel per i dispositivi, la nuova unità viene assegnata normalmente alla lettera successiva disponibile, in questo caso specifico il nuovo disco aggiunto è SDD.

4. Controllare i dischi dopo aver aggiunto il nuovo disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scenario-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scenario-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Creazione di un file System in cima al disco aggiunto di recente

    Abbinare il disco aggiunto di recente ai dispositivi collegati in/dev/disk/Azure/SCSI1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scenario-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scenario-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Creare un nuovo mountpoint temporaneo per il nuovo disco aggiunto

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Aggiungere il file System appena creato a/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Montare il nuovo FS creato con Mount-a

    ``` bash
    mount -a
    ```

9. Verificare che il nuovo FS aggiunto sia montato

    ``` bash
    df -h
    ```

    ![Resize-LVM-scenario-DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Resize-LVM-scenario-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Riavvia la crittografia avviata in precedenza per le unità dati

    Per LVM-on-crypt si consiglia di usare EncryptFormatAll in caso contrario, potrebbe verificarsi una crittografia doppia durante l'impostazione di dischi aggiuntivi.

    Per informazioni sull'utilizzo, vedere [Configure LVM on crypt](how-to-configure-lvm-raid-on-crypt.md).

    Esempio:

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

    Una volta completata la crittografia, verrà visualizzato un livello Crypt nel disco appena aggiunto.

    ``` bash
    lsblk
    ```

    ![scenario-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Smontare il livello crittografato del nuovo disco

    ``` bash
    umount ${newmount}
    ```

12. Controllare le informazioni PVS correnti

    ``` bash
    pvs
    ```

    ![scenario-currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Creare una PV sul livello crittografato del disco

    Acquisire il nome del dispositivo dal comando lsblk precedente e aggiungere/dev/mapper davanti al nome del dispositivo per creare il PV

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scenario-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Verrà visualizzato un avviso relativo alla cancellazione della firma corrente di Ext4 FS, che è prevista, rispondere y a questa domanda

14. Verificare che il nuovo PV sia stato aggiunto alla configurazione LVM

    ``` bash
    pvs
    ```

    ![scenario-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Aggiungere il nuovo PV al VG che è necessario aumentare

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scenario-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verificare le nuove dimensioni e lo spazio disponibile del VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenario-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Si noti l'aumento del numero di PE totali e del PE/dimensioni gratuite

17. Aumentare le dimensioni di LV e del file System usando l'opzione-r in lvextend (in questo esempio si sta prendendo lo spazio totale disponibile nell'VG e aggiungendolo al volume logico specificato)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scenario-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Verificare le dimensioni del LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scenario-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Verificare le dimensioni del file System appena ridimensionato

    ``` bash
    df -h mountpoint
    ```

    ![scenario-DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Verificare che il livello LVM venga creato sopra il livello crittografato

    ``` bash
    lsblk
    ```

    ![scenario-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Se si usa lsblk senza opzioni, i punti di montaggio verranno visualizzati più volte poiché vengono ordinati in base al dispositivo e ai volumi logici, è possibile usare lsblk-FS,-s per invertire l'ordinamento, in modo che il montaggio venga visualizzato una sola volta, quindi i dischi verranno visualizzati più volte.

    ``` bash
    lsblk -fs
    ```

    ![scenario-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Estensione di un LVM nel volume Crypt ridimensionamento di un PV esistente

1. Identificare i dischi crittografati

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Controllare le informazioni PV

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Controllare le informazioni sul VG

    ``` bash
    vgs
    ```

    ![scenariof-VGS](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Controllare le informazioni LV

    ``` bash
    lvs
    ```

    ![scenariof-LVS](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Controllare l'utilizzo del file System

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof-FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Controllare le dimensioni dei dischi

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Ridimensionare il disco dati

    È possibile fare riferimento a [Linux Expand disks](expand-disks.md) (solo per fare riferimento al ridimensionamento del disco). per eseguire questo passaggio, è possibile usare il portale, l'interfaccia della riga di comando o PowerShell.

    >[!NOTE]
    >Tenere presente che le operazioni di ridimensionamento sui dischi virtuali non possono essere eseguite con la macchina virtuale in esecuzione. Per questo passaggio sarà necessario deallocare la macchina virtuale

8. Controllare le dimensioni dei dischi

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Si noti che, in questo caso, entrambi i dischi sono stati ridimensionati da 2GB a 4GB, tuttavia le dimensioni di FS, LV e PV rimangono invariate.

9. Controllare le dimensioni PV correnti

    Tenere presente che in LVM-on-crypt, PV è il dispositivo/dev/mapper/, non il dispositivo/dev/sd *

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-PVS](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Ridimensionare PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof-Resize-PV](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Controllare le dimensioni PV dopo il ridimensionamento

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-PV](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Ridimensionare il livello crittografato nel PV

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Applicare la stessa procedura per tutti i dischi che si desidera ridimensionare.

13. Controllare le informazioni sul VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-VG](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    Il VG dispone ora di spazio da allocare a LVs

14. Controllare le informazioni LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Controllare l'utilizzo di FS

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof-FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Ridimensionare il LV

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Si sta usando l'opzione-r per eseguire anche il ridimensionamento di FS

17. Controllare le informazioni LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Controllare l'utilizzo del file System

    ``` bash
    df -h /mountpoint
    ```

    ![Crea filesystem](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Applicare la stessa procedura di ridimensionamento a qualsiasi LV aggiuntivo che lo richieda

## <a name="next-steps"></a>Passaggi successivi

- [Guida alla risoluzione dei problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
