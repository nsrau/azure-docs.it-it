---
title: Aggiungere un disco dati a una macchina virtuale Linux con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come aggiungere un disco dati permanente alla macchina virtuale Linux con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 81805188c72bce6a7ea89496c8036743b29e9075
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452760"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Aggiungere un disco a una VM Linux
Questo articolo illustra come collegare un disco persistente alla macchina virtuale per poter mantenere i dati, anche se si effettua di nuovo il provisioning della macchina virtuale per manutenzione o ridimensionamento.


## <a name="attach-a-new-disk-to-a-vm"></a>Collegare un nuovo disco a una VM

Se si vuole aggiungere un nuovo disco dati vuoto nella macchina virtuale, usare il comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) con il parametro `--new`. Se la macchina virtuale è in una zona di disponibilità, il disco viene creato automaticamente nella stessa area della macchina virtuale. Per ulteriori informazioni, vedere [Overview of Availability Zones](../../availability-zones/az-overview.md) (Panoramica delle zone di disponibilità in Azure). L'esempio seguente crea un disco denominato *myDataDisk* da 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --disk myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Collegare un disco esistente

Per collegare un disco esistente, individuare l'ID del disco e trasmetterlo al comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest). Nell'esempio seguente viene proposta una query per un disco denominato *myDataDisk* in *myResourceGroup*, che viene collegato alla macchina virtuale denominata *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

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

In questo caso, *sdc* è il disco interessato. Eseguire la partizione del disco con `parted`, se le dimensioni del disco sono di 2 tebibyte (TiB) o maggiori è necessario usare il partizionamento GPT, se invece sono minori di 2 TiB è possibile usare sia il partizionamento MBR che il partizionamento GPT. Se si usa il partizionamento MBR, è possibile usare `fdisk`. Renderlo un disco principale nella partizione 1 e accettare le altre impostazioni predefinite. Nell'esempio seguente viene avviato il processo `fdisk` su *dev/sdc*:

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
Usare il comando seguente per aggiornare il kernel:
```
partprobe 
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

Creare ora una directory per montare il nuovo file system usando `mkdir`. L'esempio seguente crea una directory in */datadrive*:

```bash
sudo mkdir /datadrive
```

Usare `mount` quindi per installare il file system. Nell'esempio seguente viene installata la partizione */dev/sdc1* nel punto di montaggio */datadrive*:

```bash
sudo mount /dev/sdc1 /datadrive
```

Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file */etc/fstab*. È anche consigliabile che l'UUID (Universally Unique IDentifier) usato in */etc/fstab* faccia riferimento all'unità anziché al solo nome del dispositivo, ad esempio */dev/sdc1*. Se il sistema operativo rileva un errore del disco durante l'avvio, l'uso di UUID evita che venga montato il disco non corretto in una posizione specificata. Ai dischi dati rimanenti verrebbero quindi assegnati gli stessi ID di dispositivo. Per individuare l'UUID della nuova unità, usare l'utilità `blkid`:

```bash
sudo blkid
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
>
> La Console seriale della macchina virtuale di Azure è utilizzabile per l'accesso alla console per la macchina virtuale se la modifica di fstab ha restituito un errore di avvio. Altri dettagli sono disponibili nel [documentazione della Console seriale](https://docs.microsoft.com/en-us/azure/virtual-machines/troubleshooting/serial-console-linux).

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

## <a name="troubleshooting"></a>risoluzione dei problemi

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per assicurarsi che la VM di Linux sia configurata correttamente, vedere le raccomandazioni per [ottimizzare le prestazioni della macchina virtuale di Linux](optimization.md) .
* Espandere la capacità di archiviazione aggiungendo altri dischi e [configurare RAID](configure-raid.md) per migliorare le prestazioni.
