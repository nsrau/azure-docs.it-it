---
title: Ottimizzare una macchina virtuale Linux su Azure | Microsoft Docs
description: Suggerimenti sull&quot;ottimizzazione per assicurare di configurare la VM Linux per prestazioni ottimali su Azure
keywords: linux macchina virtuale,macchina virtuale linux,macchina virtuale ubuntu
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 02b1a5bc33252a8ed16356824c33233835ab1fb7
ms.lasthandoff: 03/27/2017


---
# <a name="optimize-your-linux-vm-on-azure"></a>Ottimizzare la VM Linux su Azure
La creazione di una macchina virtuale (VM) di Linux è facile da eseguire dalla riga di comando o dal portale. Questa esercitazione illustra come assicurarsi di averla configurata in modo da ottimizzarne le prestazioni sulla piattaforma Microsoft Azure. Questo argomento usa una VM di Ubuntu Server, ma è anche possibile creare una macchina virtuale Linux usando le [proprie immagini come modelli](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Prerequisiti
Questo argomento presuppone che sia disponibile una sottoscrizione di Azure attiva ([registrazione alla versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)) e che sia già stato eseguito il provisioning di una macchina virtuale nella sottoscrizione di Azure. Assicurarsi che sia installata la versione più recente dell'[interfaccia della riga di comando 2.0 di Azure](/cli/azure/install-az-cli2) e che sia stato effettuato l'accesso alla sottoscrizione di Azure con [az login](/cli/azure/#login) prima di [creare una macchina virtuale](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco del sistema operativo di Azure
Dopo la creazione, alla macchina virtuale Linux in Azure sono associati due dischi. **/dev/sda** è il disco del sistema operativo mentre **/dev/sdb** è il disco temporaneo.  Usare il disco principale del sistema operativo (**/dev/sda**) esclusivamente per il sistema operativo, perché è ottimizzato per tempi di avvio della macchina virtuale ridotti e non offre prestazioni ideali per i carichi di lavoro. È consigliabile collegare uno o più dischi alla VM per ottenere l'archiviazione persistente e ottimizzata per i dati. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Aggiunta di dischi per risultati a livello di dimensioni e prestazioni
In base alle dimensioni della VM, è possibile collegare fino a 16 dischi aggiuntivi su una macchina virtuale di Serie A, 32 dischi su una di Serie D e 64 dischi su una di Serie G, fino a un massimo di 1 TB di dimensioni per ogni VM. Aggiungere altri dischi in base alle necessità specificate dai requisiti per spazio e IOps. Ogni disco ha un obiettivo a livello di prestazioni pari a 500 IOps per l'Archiviazione Standard e 5000 IOps per disco per l'Archiviazione Premium.  Per altre informazioni sui dischi di Archiviazione Premium vedere [Archiviazione Premium: archiviazione a prestazioni elevate per macchine virtuali di Azure](../storage/storage-premium-storage.md)

Per ottenere i valori IOps più elevati nei dischi di Archiviazione Premium in cui le impostazioni della cache sono state impostate su **ReadOnly** o **None**, è necessario disabilitare le **barriere** durante il montaggio del file system in Linux. Non sono necessarie barriere perché le scritture relative ai dischi supportati da Archiviazione Premium assicurano la durabilità per queste impostazioni della cache.

* Se si usa **reiserFS**, disabilitare le barriere tramite l'opzione di montaggio `barrier=none`. Per abilitarle usare `barrier=flush`.
* Se si usa **ext3/ext4**, disabilitare le barriere tramite l'opzione di montaggio `barrier=0`. Per abilitarle usare `barrier=1`.
* Se si usa **XFS**, disabilitare le barriere tramite l'opzione di montaggio `nobarrier`. Per abilitarle usare l'opzione `barrier`.

## <a name="unmanaged-storage-account-considerations"></a>Considerazioni sull'account di archiviazione non gestito
L'azione predefinita quando si crea una macchina virtuale con l'interfaccia della riga di comando 2.0 di Azure consiste nell'uso di Azure Managed Disks.  Questi dischi vengono gestiti dalla piattaforma Azure e non richiedono alcuna pianificazione o alcuna posizione per l'archiviazione.  I dischi non gestiti richiedono un account di archiviazione e presentano alcune considerazioni aggiuntive sulle prestazioni.  Per altre informazioni sui dischi gestiti, vedere [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Panoramica di Azure Managed Disks).  Nella sezione seguente vengono descritte le considerazioni sulle prestazioni applicabili solo quando si usano dischi non gestiti.  Anche in questo caso la soluzione di archiviazione predefinita e consigliata consiste nell'usare dischi gestiti.

Quando si crea una macchina virtuale con dischi non gestiti è consigliabile assicurarsi di collegare dischi da account di archiviazione che si trovano nella stessa area della macchina virtuale, per garantire la prossimità e ridurre al minimo la latenza di rete.  Ogni account di archiviazione Standard ha capacitò pari ad almeno 20.000 IOps e a dimensioni di 500 TB.  Ciò consente di ottenere circa 40 dischi a uso elevato, inclusi il disco del sistema operativo ed eventuali dischi dati creati. Per gli account di archiviazione Premium non sono previsti limiti massimi per IOps ma è previsto un limite di 32 TB per le dimensioni. 

Quando si gestiscono carichi di lavoro con IOps elevati ed è stata scelta l'Archiviazione Standard per i dischi, potrebbe essere necessario suddividere i dischi in più account di archiviazione, per assicurarsi di non superare il limite di 20.000 IOps per gli account di archiviazione Standard. La VM può includere una combinazione di dischi da diversi account di archiviazione e tipi di account di archiviazione per ottenere una configurazione ottimale.
 

## <a name="your-vm-temporary-drive"></a>Unità temporanea per la VM
Per impostazione predefinita, quando si crea una macchina virtuale, Azure mette a disposizione un disco del sistema operativo (**/dev/sda**) e un disco temporaneo (**/dev/sdb**).  Tutti gli altri dischi aggiuntivi verranno visualizzati come **/dev/sdc**, **/dev/sdd**, **/dev/sde** e così via. Tutti i dati nei dischi temporanei (**/dev/sdb**) non sono durevoli e possono andare persi se eventi specifici come il ridimensionamento, la ridistribuzione o la manutenzione della macchina virtuale impongono un riavvio.  Le dimensioni e il tipo del disco temporaneo sono correlati alle dimensioni della VM scelte in fase di distribuzione. Nel caso di macchine virtuali con dimensioni Premium (serie DS, G e DS_V2) l'unità temporanea sarà supportata da un'unità SSD locale per assicurare prestazioni aggiuntive fino a un massimo di 48.000 IOps. 

## <a name="linux-swap-file"></a>File di scambio Linux
Se la VM di Azure proviene da un'immagine Ubuntu o CoreOS, è possibile usare CustomData per inviare un file cloud-config a cloud-init. Se è stata [caricata un'immagine Linux personalizzata](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) che usa cloud-init, si configurano anche partizioni di scambio con cloud-init.

Nelle immagini cloud Ubuntu, è necessario usare cloud-init per configurare la partizione di scambio. Per altre informazioni vedere [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Per le immagini senza supporto cloud-init, le immagini delle VM distribuite da Azure Marketplace includono un agente Linux integrato per la VM con il sistema operativo, che consente alla VM di interagire con diversi servizi di Azure. Supponendo che sia stata distribuita un'immagine standard da Azure Marketplace, sarà necessario seguire questa procedura per configurare correttamente le impostazioni del file di scambio Linux:

Trovare e modificare due voci nel file **/etc/waagent.conf** . Queste voci controllano l'esistenza di un file di scambio dedicato e le dimensioni del file di scambio. I parametri che si sta cercando di modificare sono `ResourceDisk.EnableSwap=N` e `ResourceDisk.SwapSizeMB=0` 

Modificarli secondo le impostazioni seguenti:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={dimensione in MB che soddisfa le esigenze} 

Dopo aver apportato le modifiche, è necessario riavviare il waagent o la macchina virtuale Linux.  Per verificare l'implementazione delle modifiche e la creazione di un file di scambio, provare a usare il comando `free` per visualizzare lo spazio disponibile. L'esempio seguente consente di creare un file di scambio di 512 MB come risultato della modifica del file **waagent.conf**:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo di pianificazione I/O per l'Archiviazione Premium
Con il kernel Linux 2.6.18, l'algoritmo di pianificazione I/O predefinito è stato modificato da algoritmo Deadline a CFQ (Completely Fair Queuing). Per modelli I/O ad accesso casuale, la differenza tra le prestazioni per CFQ e Deadline è minima.  Per i dischi basati su SSD in cui il modello I/O del disco è prevalentemente sequenziale, il ritorno all'algoritmo NOOP o Deadline può consentire di ottenere prestazioni migliori per I/O.

### <a name="view-the-current-io-scheduler"></a>Visualizzare l'utilità di pianificazione di I/O corrente
Usare il comando seguente:  

```bash
cat /sys/block/sda/queue/scheduler
```

Verrà visualizzato l'output seguente, che indica l'utilità di pianificazione corrente.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Modificare il dispositivo corrente (/dev/sda) dell'algoritmo di pianificazione di I/O
Usare il seguente comando:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Applicare questa impostazione esclusivamente a **/dev/sda** non serve a niente. È necessario che sia impostata su tutti i dischi dati in cui la modalità I/O sequenziale domina il modello I/O.  

Si dovrebbe visualizzare l'output seguente, che indica che **grub.cfg** è stato ricompilato correttamente e che l'utilità di pianificazione predefinita è stata aggiornata a NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Per il gruppo di distribuzione Redhat è necessario solo il seguente comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Uso di Software RAID per ottenere valori I/Ops più elevati
Se i carichi di lavoro richiedono un valore di IOps superiore a quello consentito da un singolo disco, sarà necessario usare una configurazione Software RAID con più dischi. Poiché Azure esegue già la resilienza del disco a livello di infrastruttura locale, è possibile ottenere il livello massimo di prestazioni mediante una configurazione con striping RAID-0.  È necessario effettuare il provisioning e creare dischi nell'ambiente Azure, poi collegarli alla macchina virtuale Linux prima del partizionamento, della formattazione e del montaggio delle unità.  Altri dettagli sulla configurazione di un'installazione RAID software nella VM Linux in Azure sono disponibili nel documento **[Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**.

## <a name="next-steps"></a>Passaggi successivi
Come per tutte le considerazioni sull'ottimizzazione, sarà necessario eseguire test prima e dopo ogni modifica per misurare l'impatto della modifica stessa.  L'ottimizzazione è un processo graduale che potrà avere risultati diversi in diversi computer nell'ambiente.  Le impostazioni ottimali per una configurazione potrebbero non essere appropriate per altre.

Ecco alcuni collegamenti utili a risorse aggiuntive: 

* [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](../storage/storage-premium-storage.md)
* [Guida dell'utente dell'agente Linux di Azure](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ottimizzazione delle prestazioni di MySQL in macchine virtuali Linux di Azure](linux/classic/optimize-mysql.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Configurare RAID software in Linux](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


