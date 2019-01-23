---
title: Collegare un disco dati a una macchina virtuale Linux | Microsoft Docs
description: Usare il portale per collegare il disco dati nuovo o esistente a una macchina virtuale Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 519fd063e52d1e202ea76db0fd4be15ebd117cd0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214930"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Usare il portale per collegare un disco dati a una macchina virtuale Linux 
In questo articolo viene illustrato come collegare dischi nuovi o esistenti a una macchina virtuale Linux tramite il portale di Azure. È possibile anche [collegare un disco dati a una macchina virtuale Windows nel portale di Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Prima di collegare i dischi alla macchina virtuale, leggere i seguenti suggerimenti:

* La dimensione della macchina virtuale controlla il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Per usare l'archiviazione Premium, è necessaria una macchina virtuale della serie DS o GS. Con queste macchine virtuali, è possibile usare dischi Premium e Standard. L’archiviazione Premium è disponibile solo in determinate aree geografiche. Per informazioni dettagliate, vedere [Archiviazione Premium: Archiviazione ad alte prestazioni per i carichi di lavoro delle macchine virtuali di Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* I dischi collegati a macchine virtuali sono effettivamente file con estensione VHD archiviati in Azure. Per informazioni dettagliate, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Dopo aver collegato il disco, è necessario [connettersi alla VM Linux per montare il nuovo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Trovare la macchina virtuale
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra fare clic su **Macchine virtuali**.
3. Selezionare la macchina virtuale dall'elenco.
4. Nella pagina Macchine virtuali fare clic su **Dischi** in **Informazioni di base**.
   
    ![Aprire le impostazioni del disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Collegare un nuovo disco

1. Nel riquadro **Dischi** fare clic su **+ Aggiungi disco dati**.
2. Fare clic sul menu a discesa **Nome** e selezionare **Crea disco**:

    ![Creare un disco gestito Azure](./media/attach-disk-portal/create-new-md.png)

3. Immettere un nome per il disco gestito. Esaminare le impostazioni predefinite, aggiornare se necessario e quindi fare clic su **Crea**.
   
   ![Esaminare le impostazioni del disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Fare clic su **Salva** per creare il disco gestito e aggiornare la configurazione della macchina virtuale:

   ![Salvare il nuovo disco gestito Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Dopo che Azure crea il disco e lo collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dati**. Dal momento che i dischi gestiti sono una risorsa di livello superiore, il disco viene visualizzato nella directory principale del gruppo di risorse:

   ![Disco gestito Azure nel gruppo di risorse](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Collegare un disco esistente
1. Nel riquadro **Dischi** fare clic su **+ Aggiungi disco dati**.
2. Fare clic sul menu a discesa **Nome** per visualizzare un elenco di dischi gestiti esistenti a cui è possibile accedere con la sottoscrizione di Azure. Selezionare il disco gestito da collegare:

   ![Collegare il disco gestito di Azure](./media/attach-disk-portal/select-existing-md.png)

3. Fare clic su **Salva** per collegare il disco gestito esistente e aggiornare la configurazione della macchina virtuale:
   
   ![Salvare gli aggiornamenti del disco gestito Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Dopo che Azure collega il disco alla macchina virtuale, esso viene elencato nella sezione Impostazioni disco della macchina virtuale in **Dischi dei dati**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Connettersi alla VM Linux per montare il nuovo disco
È necessario SSH nella macchina virtuale per partizionare, formattare e montare il nuovo disco in modo che la macchina virtuale di Linux possa usarlo. Per altre informazioni, vedere [How to use SSH with Linux on Azure](mac-create-ssh-keys.md) (Come usare SSH con Linux in Azure). Nell'esempio seguente viene eseguito il collegamento a una macchina virtuale con la voce DNS pubblica di *mypublicdns.westus.cloudapp.azure.com* con il nome utente *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Una volta connessi alla macchina virtuale, si è pronti per collegare un disco. Trovare prima il disco usando `dmesg`. Il metodo usato per trovare il nuovo disco può variare. L'esempio seguente usa dmesg per applicare un filtro ai dischi *SCSI*:

```bash
dmesg | grep SCSI
```

L'output è simile all'esempio seguente:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

In questo caso, *sdc* è il disco interessato. 

### <a name="partion-a-new-disk"></a>Eseguire la partizione di un nuovo disco
Se si usa un disco esistente contenente dati, procedere al montaggio del disco. Se si collega un nuovo disco, è necessario eseguire la partizione del disco.

Eseguire la partizione del disco con `fdisk`, impostarlo come disco principale nella partizione 1 e accettare le altre impostazioni predefinite. Nell'esempio seguente viene avviato il processo `fdisk` su *dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Usare il comando `n` per aggiungere una nuova partizione. In questo esempio si sceglie il comando `p` anche per una partizione primaria e si accettano gli altri valori predefiniti. L'output sarà simile all'esempio seguente:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Stampare la tabella delle partizioni digitando `p` e usare `w` per scrivere la tabella sul disco e uscire. L'output dovrebbe essere simile all'esempio seguente:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

A questo punto, scrivere un file system nella partizione con il comando `mkfs`. Specificare il tipo di file system e il nome del dispositivo. Nell'esempio seguente viene creato un file system *ext4* nella partizione *dev/sdc1* creata nei passaggi precedenti:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

L'output è simile all'esempio seguente:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```
### <a name="mount-the-disk"></a>Montare il disco
Creare una directory per montare il file system usando `mkdir`. L'esempio seguente crea una directory in */datadrive*:

```bash
sudo mkdir /datadrive
```

Usare `mount` quindi per installare il file system. Nell'esempio seguente viene installata la partizione */dev/sdc1* nel punto di montaggio */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file */etc/fstab*. È anche consigliabile che l'UUID (Universally Unique IDentifier) usato in */etc/fstab* faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio */dev/sdc1*. Se il sistema operativo rileva un errore del disco durante l'avvio, l'uso di UUID evita che venga montato il disco non corretto in una posizione specificata. Ai dischi dati rimanenti verrebbero quindi assegnati gli stessi ID di dispositivo. Per individuare l'UUID della nuova unità, usare l'utilità `blkid`:

```bash
sudo -i blkid
```

L'output è simile al seguente esempio:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Se il file **/etc/fstab** non viene modificato in modo corretto, il sistema potrebbe diventare non avviabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

Successivamente, aprire il file */etc/fstab* in un editor di testo, come segue:

```bash
sudo vi /etc/fstab
```

In questo esempio viene usato il valore UUID del dispositivo */dev/sdc1* creato nei passaggi precedenti e il punto di montaggio di */datadrive*. Alla fine del file */etc/fstab* aggiungere la riga di codice seguente:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della VM. La maggior parte delle distribuzioni specifica le opzioni fstab *nofail* e/o *nobootwait*. Queste opzioni consentono l'avvio di un sistema anche se il montaggio del disco non riesce in fase di avvio. Per altre informazioni su questi parametri, consultare la documentazione della distribuzione.
> 
> L'opzione *nofail* garantisce che l'avvio della VM anche se il file system è danneggiato o se non è presente il disco in fase di avvio. Senza questa opzione potrebbero verificarsi comportamenti come quelli descritti in [Cannot SSH to Linux VM due to FSTAB errors](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) (Impossibile eseguire una connessione SSH a VM Linux a causa di errori FSTAB).

### <a name="trimunmap-support-for-linux-in-azure"></a>Supporto delle funzioni TRIM/UNMAP per Linux in Azure
Alcuni kernel di Linux supportano operazioni TRIM/UNMAP allo scopo di rimuovere i blocchi inutilizzati sul disco. Nel servizio di archiviazione standard, questa caratteristica è particolarmente utile per informare Azure che le pagine eliminate non sono più valide e possono essere rimosse, permettendo di risparmiare denaro se si creano file di grandi dimensioni per eliminarli successivamente.

Esistono due modi per abilitare la funzione TRIM in una VM Linux. Come di consueto, consultare la documentazione della distribuzione per stabilire l'approccio consigliato:

* Usare l'opzione di montaggio `discard` in */etc/fstab*, ad esempio:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* In alcuni casi l'opzione `discard` può avere implicazioni sulle prestazioni. In alternativa, è possibile eseguire il comando `fstrim` manualmente dalla riga di comando oppure aggiungerlo a crontab per eseguirlo a intervalli regolari:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Passaggi successivi
È anche possibile [collegare un disco dati](add-disk.md) usando l'interfaccia della riga di comando di Azure.
