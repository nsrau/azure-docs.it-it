---
title: Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: 'Esercitazione: gestire i dischi di Azure con l''interfaccia della riga di comando di Azure'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 87b410fdcd5901499e809f8d2b9a7b8788134cfc
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure

Le macchine virtuali di Azure usano dischi per archiviare sistema operativo, applicazioni e dati di macchine virtuali. Quando si crea una VM, è importante scegliere dimensione del disco e configurazione appropriate per il carico di lavoro previsto. Questa esercitazione illustra la distribuzione e la gestione dei dischi di VM. Vengono fornite informazioni su:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati
> * Ridimensionamento dei dischi
> * Snapshot dei dischi


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Dischi di Azure predefiniti

Quando viene creata una macchina virtuale di Azure, due dischi vengono automaticamente collegati alla macchina virtuale. 

**Disco del sistema operativo**: i dischi del sistema operativo possono essere ridimensionati fino a 1 terabyte e ospitano il sistema operativo delle macchine virtuali. Il disco del sistema operativo viene etichettato come */dev/sda* per impostazione predefinita. La configurazione della memorizzazione nella cache del disco del sistema operativo è ottimizzata per le prestazioni del sistema operativo. A causa di questa configurazione, il disco del sistema operativo **non deve** ospitare applicazioni o i dati. Per le applicazioni e i dati, usare un disco dati, descritto in dettaglio più avanti in questo articolo. 

**Disco temporaneo**: i dischi temporanei usano un'unità SSD che si trova nello stesso host della macchina virtuale di Azure. I dischi temporanei sono altamente efficienti e possono essere usati per operazioni quali l'elaborazione dei dati temporanei. Tuttavia, se la macchina virtuale viene spostata in un nuovo host, tutti i dati memorizzati su un disco temporaneo vengono rimossi. Le dimensioni del disco temporaneo sono determinate dalle dimensioni della macchina virtuale. I dischi temporanei vengono etichettati come */dev/sdb* e hanno un punto di montaggio */mnt*.

### <a name="temporary-disk-sizes"></a>Dimensioni del disco temporaneo

| type | Dimensioni macchina virtuale | Dimensioni massime del disco temporaneo (GB) |
|----|----|----|
| [Utilizzo generico](sizes-general.md) | Serie A e D | 800 |
| [Ottimizzate per il calcolo](sizes-compute.md) | Serie F | 800 |
| [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md) | Serie D e G | 6144 |
| [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md) | Serie L | 5630 |
| [GPU](sizes-gpu.md) | Serie N | 1.440 |
| [Prestazioni elevate](sizes-hpc.md) | Serie A e H | 2000 |

## <a name="azure-data-disks"></a>Dischi dati di Azure

È possibile aggiungere altri dischi dati per l'installazione di applicazioni e l'archiviazione dei dati. I dischi dati devono essere usati in qualsiasi situazione in cui si desidera un'archiviazione dei dati durevoli e reattiva. Ciascun disco dati ha una capacità massima di 1 terabyte. Le dimensione della macchina virtuale determinano il numero di dischi dati possono essere collegati a una macchina virtuale. Per ogni vCPU della macchina virtuale, è possibile collegare due dischi dati. 

### <a name="max-data-disks-per-vm"></a>Numero massimo di dischi di dati per macchina virtuale

| type | Dimensioni macchina virtuale | Numero massimo di dischi di dati per macchina virtuale |
|----|----|----|
| [Utilizzo generico](sizes-general.md) | Serie A e D | 32 |
| [Ottimizzate per il calcolo](sizes-compute.md) | Serie F | 32 |
| [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md) | Serie D e G | 64 |
| [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md) | Serie L | 64 |
| [GPU](sizes-gpu.md) | Serie N | 48 |
| [Prestazioni elevate](sizes-hpc.md) | Serie A e H | 32 |

## <a name="vm-disk-types"></a>Tipi di dischi per la VM

Azure offre due tipi di dischi.

### <a name="standard-disk"></a>Disco standard

Archiviazione Standard è supportata da unità disco rigido e offre un'archiviazione conveniente con buone prestazioni. I dischi standard sono ideali per un carico di lavoro di test e sviluppo conveniente.

### <a name="premium-disk"></a>Disco premium

I dischi premium sono supportati da un disco a bassa latenza e ad alte prestazioni basato su SSD. Ideale per le macchine virtuali che eseguono il carico di lavoro della produzione. L'archiviazione premium supporta le macchine virtuali serie DS, DSv2, GS e FS. I dischi premium sono di tre tipi, P10, P20 e P30. Le dimensioni del disco determinano il tipo di disco. Quando si effettua la selezione, il valore delle dimensioni di un disco viene arrotondato per eccesso al tipo successivo. Ad esempio, se le dimensioni del disco non superano i 128 GB, il tipo di disco è P10. Se le dimensioni del disco vanno da 129 a 512 GB, il tipo sarà un P20. Un valore superiore a 512 GB determina un tipo di disco P30.

### <a name="premium-disk-performance"></a>Prestazioni disco premium

|Tipo di disco di Archiviazione Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Dimensioni del disco (arrotondate) | 128 GB | 512 GB | 1.024 GB (1 TB) |
| Operazioni IOPS al secondo max per disco | 500 | 2.300 | 5.000 |
Velocità effettiva per disco | 100 MB/s | 150 MB/s | 200 MB/s |

Sebbene la tabella sopra riportata identifichi il numero massimo di operazioni di I/O al secondo per disco, è possibile raggiungere un livello superiore di prestazioni tramite lo striping di più dischi di dati. Ad esempio, una macchina virtuale Standard_GS5 può raggiungere un massimo di 80.000 operazioni di I/O al secondo. Per informazioni dettagliate sul numero massimo di operazioni di I/O al secondo per macchina virtuale, vedere [Dimensioni delle VM Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Creare e collegare dischi

I dischi dati possono essere creati e collegati al momento della creazione della macchina virtuale o a una macchina virtuale esistente.

### <a name="attach-disk-at-vm-creation"></a>Collegare un disco al momento della creazione della macchina virtuale

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Creare una VM con il comando [az vm create]( /cli/azure/vm#az_vm_create). L'argomento `--datadisk-sizes-gb` viene usato per specificare che è necessario creare un disco aggiuntivo e collegarlo alla macchina virtuale. Per creare e collegare più dischi, usare un elenco delimitato da spazio dei valori delle dimensioni del disco. Nell'esempio seguente viene creata una macchina virtuale con due dischi di dati, entrambi di 128 GB. Poiché le dimensioni dei dischi sono 128 GB, entrambi i dischi sono configurati come P10, il che garantisce un massimo di 500 operazioni di I/O al secondo per disco.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Collegare un disco alla macchina virtuale esistente

Per creare e collegare un nuovo disco a una macchina virtuale esistente, usare il comando [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach). Nell'esempio seguente viene creato un disco premium, di 128 gigabyte, che viene collegato alla macchina virtuale creata nel passaggio precedente.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Preparare i dischi di dati

Dopo aver collegato un disco alla macchina virtuale, il sistema operativo deve essere configurato per l'uso del disco. L'esempio seguente illustra come configurare manualmente un disco. È anche possibile automatizzare questo processo puòcon cloud-init, di cui si parlerà nell'[esercitazione successiva](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configurazione manuale

Creare una connessione SSH alla macchina virtuale. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale.

```azurecli-interactive 
ssh 52.174.34.95
```

Eseguire la partizione del disco con `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Scrivere un file system nella partizione con il comando `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montare il nuovo disco in modo che sia accessibile nel sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

È ora possibile accedere al disco tramite il punto di montaggio *datadrive*, che può essere verificato con l'esecuzione del comando `df -h`. 

```bash
df -h
```

L'output mostra la nuova unità montata su */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Per assicurarsi che l'unità venga rimontata dopo un riavvio, è necessario aggiungerla al file */etc/fstab*. A tale scopo, ottenere l'UUID del disco con l'utilità `blkid`.

```bash
sudo -i blkid
```

L'output mostra l'UUID dell'unità, `/dev/sdc1` in questo caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Aggiungere una riga simile alla seguente al file */etc/fstab*. Si noti anche che le barriere di scrittura possono essere disabilitate mediante *barrier=0*; questa configurazione può migliorare le prestazioni del disco. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Ora che il disco è stato configurato, chiudere la sessione SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Ridimensionare il disco della macchina virtuale

Dopo la distribuzione di una macchina virtuale, è possibile aumentare le dimensioni del disco del sistema operativo o dei dischi dati collegati. L'aumento delle dimensioni di un disco è utile quando è necessario un maggiore spazio di archiviazione o un livello superiore di prestazioni, ad esempio P10, P20 e P30. Si noti che non è possibile diminuire le dimensioni dei dischi.

Prima di aumentare le dimensioni del disco, è necessario conoscere l'ID o il nome del disco. Usare il comando [az disk list](/cli/azure/disk#az_disk_list) per restituire tutti i dischi in un gruppo di risorse. Prendere nota del nome del disco di cui si desidera cambiare le dimensioni.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

La macchina virtuale deve anche essere deallocata. Usare il comando [aaz vm deallocate]( /cli/azure/vm#az_vm_deallocate) per arrestare e deallocare la VM.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Usare il comando [az disk update](/cli/azure/vm/disk#az_vm_disk_update) per ridimensionare il disco. In questo esempio un disco denominato *myDataDisk* viene ridimensionato a 1 terabyte.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Dopo aver completato l'operazione di ridimensionamento, avviare la macchina virtuale.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Se è stato ridimensionato il disco del sistema operativo, la partizione si espande automaticamente. Se è stato ridimensionato un disco dati, tutte le partizioni correnti devono essere estese al sistema operativo delle macchine virtuali.

## <a name="snapshot-azure-disks"></a>Snapshot di dischi di Azure

Quando si crea uno snapshot del disco, viene creata una copia temporizzata di sola lettura del disco. Gli snapshot della macchina virtuale di Azure sono utili per salvare rapidamente lo stato di una macchina virtuale prima di apportare modifiche alla configurazione. Nel caso in cui le modifiche alla configurazione si rivelino non volute, lo stato della macchina virtuale può essere ripristinato con lo snapshot. Quando una macchina virtuale dispone di più dischi, viene acquisito uno snapshot per ciascun disco separatamente dagli altri dischi. Per eseguire backup coerenti con l'applicazione, prendere in considerazione l'arresto della macchina virtuale prima di eseguire gli snapshot dei dischi. In alternativa, usare il [Servizio Backup di Microsoft Azure](/azure/backup/), che consente di eseguire backup automatici, mentre la macchina virtuale è in esecuzione.

### <a name="create-snapshot"></a>Creare uno snapshot

Prima di creare uno snapshot del disco della macchina virtuale, è necessario conoscere l'ID o il nome del disco. Usare il comando [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) per ottenere l'ID del disco. In questo esempio l'ID del disco viene archiviato in una variabile in modo da poter essere usato in un passaggio successivo.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Dopo aver ottenuto l'ID del disco della macchina virtuale, usare il comando seguente per creare lo snapshot del disco.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Creare un disco da uno snapshot

Lo snapshot può quindi essere convertito in un disco da usare per ricreare la macchina virtuale.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Ripristinare una macchina virtuale da uno snapshot

Per illustrare il ripristino della macchina virtuale, eliminare la macchina virtuale esistente. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Creare una nuova macchina virtuale dal disco dello snapshot.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Ricollegare un disco dati

È necessario ricollegare tutti i dischi dati alla macchina virtuale.

Innanzitutto usare il comando [az disk list](https://docs.microsoft.com/cli/azure/disk#az_disk_list) per trovare il nome del disco dati. L'esempio inserisce il nome del disco in una variabile denominata *datadisk*, che viene usata nel passaggio successivo.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Usare il comando [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) per collegare il disco.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati descritti argomenti relativi ai dischi delle VM, ad esempio:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati
> * Ridimensionamento dei dischi
> * Snapshot dei dischi

Passare all'esercitazione successiva per informazioni sull'automazione della configurazione delle macchine virtuali.

> [!div class="nextstepaction"]
> [Automatizzare la configurazione delle macchine virtuali](./tutorial-automate-vm-deployment.md)
