---
title: "Esercitazione: gestire i dischi di Azure con l'interfaccia della riga di comando di Azure"
description: In questa esercitazione viene descritto come usare l'interfaccia della riga di comando di Azure per creare e gestire dischi di Azure per macchine virtuali
author: cynthn
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 948a4ae8c329d69e404ef8d0f609748b955b0ecc
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078850"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Esercitazione: gestire i dischi di Azure con l'interfaccia della riga di comando di Azure

Le macchine virtuali (VM) di Azure usano dischi per archiviare sistema operativo, applicazioni e dati. Quando si crea una VM, è importante scegliere dimensioni del disco e configurazione appropriate per il carico di lavoro previsto. Questa esercitazione illustra come distribuire e gestire i dischi delle macchine virtuali. Vengono fornite informazioni su:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati
> * Snapshot dei dischi


## <a name="default-azure-disks"></a>Dischi di Azure predefiniti

Quando viene creata una macchina virtuale di Azure, due dischi vengono automaticamente collegati alla macchina virtuale.

**Disco del sistema operativo**: i dischi del sistema operativo possono essere ridimensionati fino a 2 terabyte e ospitano il sistema operativo delle macchine virtuali. Il disco del sistema operativo viene etichettato come */dev/sda* per impostazione predefinita. La configurazione della memorizzazione nella cache del disco del sistema operativo è ottimizzata per le prestazioni del sistema operativo. A causa di questa configurazione, il disco del sistema operativo **non deve** essere usato per applicazioni o dati. Per le applicazioni e i dati, usare un disco dati, descritto in dettaglio più avanti in questa esercitazione.

**Disco temporaneo**: i dischi temporanei usano un'unità SSD che si trova nello stesso host della macchina virtuale di Azure. I dischi temporanei sono altamente efficienti e possono essere usati per operazioni quali l'elaborazione dei dati temporanei. Tuttavia, se la macchina virtuale viene spostata in un nuovo host, tutti i dati memorizzati su un disco temporaneo vengono rimossi. Le dimensioni del disco temporaneo sono determinate dalle dimensioni della macchina virtuale. I dischi temporanei vengono etichettati come */dev/sdb* e hanno un punto di montaggio */mnt*.

## <a name="azure-data-disks"></a>Dischi dati di Azure

Per installare applicazioni e archiviare dati è possibile aggiungere altri dischi dati. I dischi dati devono essere usati in qualsiasi situazione in cui si desidera un'archiviazione dei dati durevoli e reattiva. Le dimensione della macchina virtuale determinano il numero di dischi dati possono essere collegati a una macchina virtuale.

## <a name="vm-disk-types"></a>Tipi di dischi per la VM

Azure offre due tipi di dischi.

**Dischi Standard**: sono supportati da unità disco rigido e offrono un'archiviazione conveniente con buone prestazioni. I dischi standard sono ideali per un carico di lavoro di test e sviluppo conveniente.

**Dischi Premium**: sono supportati da un disco a bassa latenza e ad alte prestazioni basato su unità SSD. Ideale per le macchine virtuali che eseguono il carico di lavoro della produzione. Le dimensioni di macchina virtuale che contengono la lettera **S** nel [nome delle dimensioni](../vm-naming-conventions.md) in genere supportano Archiviazione Premium. Le macchine virtuali di Azure serie DS, DSv2 e GS e FS, ad esempio, supportano Archiviazione Premium. Quando si selezionano le dimensioni del disco, il valore viene arrotondato per eccesso al tipo successivo. Ad esempio, se le dimensioni del disco superano i 64 GB ma sono inferiori a 128 GB, il tipo di disco è P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni. I dischi SSD Premium sono progettati per offrire basse latenze di pochissimi millisecondi, nonché il numero di operazioni di I/O al secondo e la velocità effettiva di destinazione descritti nella tabella precedente per il 99,9% del tempo.

Sebbene la tabella sopra riportata identifichi il numero massimo di operazioni di I/O al secondo per disco, è possibile raggiungere un livello superiore di prestazioni tramite lo striping di più dischi di dati. Ad esempio, è possibile collegare 64 dati dischi alla macchina virtuale Standard_GS5. Se ognuno di questi dischi viene ridimensionato come un P30, è possibile ottenere un massimo di 80.000 operazioni di I/O al secondo. Per informazioni dettagliate sul numero massimo di operazioni di I/O al secondo per macchina virtuale, vedere [Tipi e dimensioni delle macchine virtuali](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, selezionare **Prova** nell'angolo in alto a destra di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="create-and-attach-disks"></a>Creare e collegare dischi

I dischi dati possono essere creati e collegati al momento della creazione della macchina virtuale o a una macchina virtuale esistente.

### <a name="attach-disk-at-vm-creation"></a>Collegare un disco al momento della creazione della macchina virtuale

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Creare una VM con il comando [az vm create](/cli/azure/vm#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM*, aggiunge un account utente denominato *azureuser* e genera le chiavi SSH, se non esistono già. L'argomento `--datadisk-sizes-gb` viene usato per specificare che è necessario creare un disco aggiuntivo e collegarlo alla macchina virtuale. Per creare e collegare più dischi, usare un elenco delimitato da spazio dei valori delle dimensioni del disco. Nell'esempio seguente viene creata una macchina virtuale con due dischi di dati, entrambi di 128 GB. Poiché le dimensioni dei dischi sono 128 GB, entrambi i dischi sono configurati come P10, il che garantisce un massimo di 500 operazioni di I/O al secondo per disco.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Collegare un disco alla macchina virtuale esistente

Per creare e collegare un nuovo disco a una macchina virtuale esistente, usare il comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). Nell'esempio seguente viene creato un disco premium, di 128 gigabyte, che viene collegato alla macchina virtuale creata nel passaggio precedente.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Preparare i dischi di dati

Dopo aver collegato un disco alla macchina virtuale, il sistema operativo deve essere configurato per l'uso del disco. L'esempio seguente illustra come configurare manualmente un disco. È anche possibile automatizzare questo processo puòcon cloud-init, di cui si parlerà nell'[esercitazione successiva](./tutorial-automate-vm-deployment.md).


Creare una connessione SSH alla macchina virtuale. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale.

```console
ssh 10.101.10.10
```

Eseguire la partizione del disco con `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Scrivere un file system nella partizione con il comando `mkfs`. Usare `partprobe` per fare in modo che il sistema operativo riconosca la modifica.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Montare il nuovo disco in modo che sia accessibile nel sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

È possibile ora accedere al disco tramite il punto di montaggio `/datadrive`, che è possibile verificare eseguendo il comando `df -h`.

```bash
df -h | grep -i "sd"
```

L'output mostra la nuova unità montata su `/datadrive`.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Per assicurarsi che l'unità venga rimontata dopo un riavvio, è necessario aggiungerla al file */etc/fstab*. A tale scopo, ottenere l'UUID del disco con l'utilità `blkid`.

```bash
sudo -i blkid
```

L'output mostra l'UUID dell'unità, `/dev/sdc1` in questo caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> Se il file **/etc/fstab** non viene modificato in modo corretto, il sistema potrebbe diventare non avviabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

Aprire il file `/etc/fstab` in un editor di testo, come segue:

```bash
sudo nano /etc/fstab
```

Aggiungere una riga simile alla seguente al file */etc/fstab*, sostituendo il valore UUID con quello personale.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Dopo aver completato la modifica del file, usare `Ctrl+O` per scrivere il file e `Ctrl+X` per uscire dall'editor.

Ora che il disco è stato configurato, chiudere la sessione SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Acquisire uno snapshot del disco

Quando si crea uno snapshot del disco, Azure crea una copia temporizzata di sola lettura del disco. Gli snapshot della macchina virtuale di Azure sono utili per salvare rapidamente lo stato di una macchina virtuale prima di apportare modifiche alla configurazione. In caso di problemi o errori, è possibile ripristinare la macchina virtuale usando uno snapshot. Quando una macchina virtuale dispone di più dischi, viene acquisito uno snapshot per ciascun disco separatamente dagli altri dischi. Per eseguire backup coerenti con l'applicazione, prendere in considerazione l'arresto della macchina virtuale prima di eseguire gli snapshot dei dischi. In alternativa, usare il [Servizio Backup di Microsoft Azure](../../backup/index.yml), che consente di eseguire backup automatici, mentre la macchina virtuale è in esecuzione.

### <a name="create-snapshot"></a>Create snapshot

Prima di creare uno snapshot, è necessario l'ID o il nome del disco. Usare [az vm show](/cli/azure/vm#az-vm-show) per visualizzare l'ID disco. In questo esempio l'ID del disco viene archiviato in una variabile in modo da poter essere usato in un passaggio successivo.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Ora che l'ID è disponibile, usare [az snapshot create](/cli/azure/snapshot#az-snapshot-create) per creare uno snapshot del disco.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Creare un disco da uno snapshot

È quindi possibile usare [az disk create](/cli/azure/disk#az-disk-create) per convertire lo snapshot in un disco, che può essere usato per ricreare la macchina virtuale.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Ripristinare una macchina virtuale da uno snapshot

Per illustrare il ripristino della macchina virtuale, usare [az vm delete](/cli/azure/vm#az-vm-delete) per eliminare la macchina virtuale esistente.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Creare una nuova macchina virtuale dal disco dello snapshot.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Ricollegare un disco dati

È necessario ricollegare tutti i dischi dati alla macchina virtuale.

Usare il comando [az disk list](/cli/azure/disk#az-disk-list) per trovare il nome del disco dati. L'esempio inserisce il nome del disco in una variabile denominata `datadisk`, che viene usata nel passaggio successivo.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Usare il comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) per collegare il disco.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati descritti argomenti relativi ai dischi delle VM, ad esempio:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegamento e preparazione dei dischi dati
> * Snapshot dei dischi

Passare all'esercitazione successiva per informazioni sull'automazione della configurazione delle macchine virtuali.

> [!div class="nextstepaction"]
> [Automatizzare la configurazione delle macchine virtuali](./tutorial-automate-vm-deployment.md)
