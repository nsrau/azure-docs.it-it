---
title: Aggiungere un disco dati a una VM Linux usando l'interfaccia della riga di comando di Azure
description: Informazioni su come aggiungere un disco dati permanente alla macchina virtuale Linux con l'interfaccia della riga di comando di Azure
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 9d04e28c4af462719644deca4c4aa0e3aa94fa16
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757728"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Aggiungere un disco a una VM Linux

Questo articolo illustra come collegare un disco persistente alla macchina virtuale per poter mantenere i dati, anche se si effettua di nuovo il provisioning della macchina virtuale per manutenzione o ridimensionamento.


## <a name="attach-a-new-disk-to-a-vm"></a>Collegare un nuovo disco a una VM

Se si vuole aggiungere un nuovo disco dati vuoto nella macchina virtuale, usare il comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest) con il parametro `--new`. Se la macchina virtuale è in una zona di disponibilità, il disco viene creato automaticamente nella stessa area della macchina virtuale. Per ulteriori informazioni, vedere [Overview of Availability Zones](../../availability-zones/az-overview.md) (Panoramica delle zone di disponibilità in Azure). L'esempio seguente crea un disco denominato *myDataDisk* da 50 GB:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Collegare un disco esistente

Per collegare un disco esistente, individuare l'ID del disco e trasmetterlo al comando [az vm disk attach](/cli/azure/vm/disk?view=azure-cli-latest). Nell'esempio seguente viene proposta una query per un disco denominato *myDataDisk* in *myResourceGroup*, che viene collegato alla macchina virtuale denominata *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formattare e montare il disco

È necessario SSH nella macchina virtuale per partizionare, formattare e montare il nuovo disco in modo che la macchina virtuale di Linux possa usarlo. Per altre informazioni, vedere [How to use SSH with Linux on Azure](mac-create-ssh-keys.md) (Come usare SSH con Linux in Azure). Nell'esempio seguente viene eseguita la connessione a una macchina virtuale con l'indirizzo IP pubblico di *10.123.123.25* con il nome utente *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Trovare il disco

Una volta connessi alla macchina virtuale, è necessario trovare il disco. In questo esempio viene usato `lsblk` per elencare i dischi. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

L'output è simile all'esempio seguente:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Qui, `sdc` è il disco desiderato, perché è 50g. Se non si è certi di quale disco sia basato solo sulle dimensioni, è possibile passare alla pagina VM nel portale, selezionare **dischi**e verificare il numero di lun del disco in **dischi dati**. 


### <a name="format-the-disk"></a>Formattare il disco

Formattare il disco con `parted` , se la dimensione del disco è 2 TB (tib) o superiore, è necessario usare il partizionamento GPT, se si trova in 2TiB, è possibile usare il partizionamento MBR o GPT. 

> [!NOTE]
> Si consiglia di usare la versione più recente `parted` disponibile per la distribuzione.
> Se la dimensione del disco è 2 TB (TiB) o superiore, è necessario utilizzare il partizionamento GPT. Se la dimensione del disco è inferiore a 2 TiB, è possibile usare il partizionamento MBR o GPT.  


Nell'esempio seguente viene usato `parted` on `/dev/sdc` , dove il primo disco dati sarà in genere nella maggior parte delle macchine virtuali. Sostituire `sdc` con l'opzione corretta per il disco. Viene anche formattato con il file System [XFS](https://xfs.wiki.kernel.org/) .

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Usare l' [`partprobe`](https://linux.die.net/man/8/partprobe) utilità per assicurarsi che il kernel sia in grado di riconoscere la nuova partizione e il file System. La mancata utilizzo `partprobe` può causare la restituzione immediata dell'UUID per il nuovo file System da parte dei comandi blkid o lslbk.


### <a name="mount-the-disk"></a>Montare il disco

Creare ora una directory per montare il nuovo file system usando `mkdir`. Nell'esempio seguente viene creata una directory in `/datadrive` :

```bash
sudo mkdir /datadrive
```

Usare `mount` quindi per installare il file system. Nell'esempio seguente la partizione viene montata nel `/dev/sdc1` `/datadrive` punto di montaggio:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Rendere permanente il montaggio

Per assicurarsi che l'unità venga rimontata automaticamente dopo un riavvio, è necessario aggiungerla al file */etc/fstab*. Si consiglia inoltre di usare UUID (Universally Unique Identifier) in */etc/fstab* per fare riferimento all'unità anziché al solo nome del dispositivo (ad esempio, */dev/sdc1*). Se il sistema operativo rileva un errore del disco durante l'avvio, l'uso di UUID evita che venga montato il disco non corretto in una posizione specificata. Ai dischi dati rimanenti verrebbero quindi assegnati gli stessi ID di dispositivo. Per individuare l'UUID della nuova unità, usare l'utilità `blkid`:

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
sudo nano /etc/fstab
```

In questo esempio, usare il valore UUID per il `/dev/sdc1` dispositivo creato nei passaggi precedenti e il mountpoint di `/datadrive` . Aggiungere la riga seguente alla fine del `/etc/fstab` file:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

In questo esempio viene usato l'editor nano, quindi, dopo aver completato la modifica del file, usare `Ctrl+O` per scrivere il file e `Ctrl+X` uscire dall'editor.

> [!NOTE]
> Se si rimuove successivamente un disco dati senza modificare fstab, è possibile che si verifichi un errore di avvio della VM. La maggior parte delle distribuzioni specifica le opzioni fstab *nofail* e/o *nobootwait*. Queste opzioni consentono l'avvio di un sistema anche se il montaggio del disco non riesce in fase di avvio. Per altre informazioni su questi parametri, consultare la documentazione della distribuzione.
>
> L'opzione *nofail* garantisce che l'avvio della VM anche se il file system è danneggiato o se non è presente il disco in fase di avvio. Senza questa opzione potrebbero verificarsi comportamenti come quelli descritti in [Cannot SSH to Linux VM due to FSTAB errors](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) (Impossibile eseguire una connessione SSH a VM Linux a causa di errori FSTAB).
>
> La console seriale di VM di Azure può essere usata per l'accesso alla console alla VM se la modifica di fstab ha causato un errore di avvio. Ulteriori informazioni sono disponibili nella [documentazione della console seriale](../troubleshooting/serial-console-linux.md).

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

## <a name="troubleshooting"></a>Risoluzione dei problemi

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per assicurarsi che la VM di Linux sia configurata correttamente, vedere le raccomandazioni per [ottimizzare le prestazioni della macchina virtuale di Linux](optimization.md) .
* Espandere la capacità di archiviazione aggiungendo altri dischi e [configurare RAID](configure-raid.md) per migliorare le prestazioni.
