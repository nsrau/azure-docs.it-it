---
title: Configurare RAID software in una macchina virtuale che esegue Linux | Microsoft Docs
description: Informazioni su come usare mdadm per configurare RAID in Linux in Azure.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: timlt
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: e64f972759173965d389b694ada23720d1182bb8
ms.openlocfilehash: 92a6c849f5b28581fcac3713e9756dc06d7a251b


---
# <a name="configure-software-raid-on-linux"></a>Configurare RAID software in Linux
I RAID software vengono spesso usati nelle macchine virtuali Linux in Azure per presentare più dischi dati collegati come se si trattasse di un singolo dispositivo RAID. In genere questa configurazione consente di migliorare le prestazioni e la velocità effettiva rispetto all'utilizzo di un unico disco.

## <a name="attaching-data-disks"></a>Collegamento di dischi dati
Per configurare un dispositivo RAID sono necessari due o più dischi dati vuoti.  Il dispositivo RAID viene creato principalmente per migliorare le prestazioni dell'I/O su disco.  In base alle esigenze di I/O, è possibile scegliere di collegare dischi che sono archiviati nell'archiviazione Standard con un massimo di 500 IO/ps per ogni disco o nell'archiviazione Premium con un massimo di 5.000 IO/ps per ogni disco. In questo articolo non verrà illustrato in dettaglio come eseguire il provisioning e collegare dischi dati a una macchina virtuale Linux.  Per istruzioni dettagliate su come collegare un disco dati vuoto a una macchina virtuale Linux in Azure, vedere l'articolo di Microsoft Azure relativo al [collegamento di dischi](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="install-the-mdadm-utility"></a>Installazione dell'utility mdadm
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS e Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES e openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>Creazione delle partizioni del disco
In questo esempio verrà creata una singola partizione del disco in /dev/sdc. La nuova partizione del disco verrà denominata /dev/sdc1.

1. Avviare `fdisk` per iniziare la creazione delle partizioni

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. Premere 'n' al prompt per creare una **n**uova partizione:

    ```bash
    Command (m for help): n
    ```

3. Successivamente, premere 'p' per creare una partizione **p**rimaria:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. Premere '1' per selezionare la partizione numero 1:

    ```bash
    Partition number (1-4): 1
    ```

5. Selezionare il punto di inizio della nuova partizione oppure premere `<enter>` per accettare le impostazioni predefinite, che prevedono il posizionamento della partizione all'inizio dello spazio libero nell'unità:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. Selezionare le dimensioni della partizione, ad esempio digitare '+10G' per creare una partizione da 10 gigabyte. In alternativa, premere `<enter>` per creare un'unica partizione che occupa l'intera unità:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. Successivamente, modificare l'ID e il **t**ipo della partizione dal valore predefinito '83' (Linux) a 'fd' (rilevamento automatico RAID Linux):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. Infine, scrivere la tabella delle partizioni sull'unità e chiudere fdisk:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>Creazione dell'array RAID
1. Nell'esempio seguente verrà eseguito lo striping (livello RAID 0) di tre partizioni situate in tre dischi dati separati (sdc1, sdd1, sde1).  Dopo l'esecuzione del comando verrà creato un nuovo dispositivo RAID denominato **/dev/md127** . Si noti anche che se i dischi dati facevano precedentemente parte di una matrice RAID inattiva, può essere necessario aggiungere il parametro `--force` al comando `mdadm`:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. Creare il file system nel nuovo dispositivo RAID
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    b. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    c. **SLES 11 & openSUSE** - abilitare boot.md e creare mdadm.conf

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > Dopo aver apportato queste modifiche nei sistemi SUSE può essere necessario il riavvio. Questo passaggio *non* è obbligatorio su SLES 12.
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>Aggiungere il nuovo file a /etc/fstab
> [!IMPORTANT]
> Se il file /etc/fstab non viene modificato in modo corretto, il sistema potrebbe diventare instabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

1. Creare il punto di montaggio desiderato per il nuovo file system, ad esempio:

    ```bash
    sudo mkdir /data
    ```
2. Quando si modifica /etc/fstab è consigliabile utilizzare l' **UUID** anziché il nome del dispositivo per fare riferimento al file system.  Usare l'utilità `blkid` per determinare l'UUID per il nuovo file system:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. Aprire /etc/fstab in un editor di testo e aggiungere una voce per il nuovo file system, ad esempio:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    O su **SLES 11 & openSUSE**:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Salvare e chiudere /etc/fstab.

4. Verificare che la voce /etc/fstab sia corretta:

    ```bash  
    sudo mount -a
    ```

    Se questo comando genera un messaggio di errore, verificare la sintassi nel file /etc/fstab file.
   
    Eseguire quindi il comando `mount` per assicurarsi che il file system venga montato:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (Facoltativo) Parametri di avvio alternativo
   
    **Configurazione di fstab**
   
    Molte distribuzioni includono i parametri di montaggio `nobootwait` o `nofail`, che è possibile aggiungere al file /etc/fstab. Tali parametri consentono di ignorare gli errori durante il montaggio di uno specifico file system. Consentono pertanto di proseguire l'avvio del sistema Linux anche se non è possibile montare correttamente il file system RAID. Per altre informazioni su questi parametri, fare riferimento alla documentazione della distribuzione.
   
    Esempio (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Parametri di avvio di Linux**
   
    Oltre ai parametri precedenti, il parametro del kernel "`bootdegraded=true`" consente di avviare il sistema anche se il RAID viene percepito come danneggiato o con funzionalità ridotte, ad esempio se un'unità dati viene rimossa accidentalmente dalla macchina virtuale. Per impostazione predefinita, questa situazione può rendere impossibile l'avvio del sistema.
   
    Per informazioni sulla corretta modifica dei parametri del kernel, fare riferimento alla documentazione della distribuzione. Ad esempio, in molte distribuzioni (CentOS, Oracle Linux, SLES 11) è possibile aggiungere manualmente tali parametri al file "`/boot/grub/menu.lst`".  In Ubuntu è possibile aggiungere il parametro `GRUB_CMDLINE_LINUX_DEFAULT` alla variabile in "/etc/default/grub".


## <a name="trimunmap-support"></a>Supporto per TRIM/UNMAP
Alcuni kernel di Linux supportano operazioni TRIM/UNMAP allo scopo di rimuovere i blocchi inutilizzati sul disco. Nel servizio di archiviazione standard, queste operazioni sono particolarmente utili per informare Azure che le pagine eliminate non sono più valide e possono essere rimosse. L'eliminazione delle pagine consente di risparmiare sui costi quando si creano file di grandi dimensioni per poi eliminarli.

> [!NOTE]
> RAID non può inviare comandi di rimozione se le dimensioni del blocco per la matrice sono impostate su un valore inferiore a quello predefinito di 512 KB. Questo perché anche la granularità di annullamento del mapping nell'host è di 512 KB. Se le dimensioni del blocco della matrice sono state modificate tramite il parametro `--chunk=` di mdadm, il kernel può ignorare le richieste TRIM/UNMAP.

Esistono due modi per abilitare la funzione TRIM in una VM Linux. Come di consueto, consultare la documentazione della distribuzione per stabilire l'approccio consigliato:

- Usare l'opzione di montaggio `discard` in `/etc/fstab`, ad esempio:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- In alcuni casi l'opzione `discard` può avere implicazioni sulle prestazioni. In alternativa, è possibile eseguire il comando `fstrim` manualmente dalla riga di comando oppure aggiungerlo a crontab per eseguirlo a intervalli regolari:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```



<!--HONumber=Dec16_HO1-->


