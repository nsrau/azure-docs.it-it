---
title: Esercitazione - Creare e gestire un set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come usare l'interfaccia della riga di comando di Azure 2.0 per creare un set di scalabilità di macchine virtuali, con alcune attività di gestione comuni come l'avvio e l'arresto di un'istanza o la modifica della capacità del set di scalabilità.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dc8c58efcaeb5491eb23257e470f42a8d7cfd5c1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Esercitazione: Creare e gestire un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure 2.0
Un set di scalabilità di macchine virtuali consente di distribuire e gestire un set di macchine virtuali identiche con scalabilità automatica. Nel ciclo di vita del set di scalabilità di una macchina virtuale potrebbe essere necessario eseguire una o più attività di gestione. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un set di scalabilità di macchine virtuali e connettersi
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare dimensioni di istanze di VM specifiche
> * Ridimensionare manualmente un set di scalabilità
> * Eseguire attività comuni per la gestione dei set di scalabilità

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. È necessario creare un gruppo di risorse prima di un set di scalabilità di macchine virtuali. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az_group_create). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

In questa esercitazione, il nome del gruppo di risorse viene specificato quando si crea o si modifica un set di scalabilità.


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Un set di scalabilità di macchine virtuali viene creato con il comando [az vmss create](/cli/azure/vmss#az_vmss_create). Nell'esempio seguente viene creato un set di scalabilità denominato *myScaleSet* e vengono generate le chiavi SSH, se non sono presenti:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti. Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Visualizzare le istanze di VM in un set di scalabilità
Per visualizzare un elenco delle istanze di VM in un set di scalabilità, usare [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) come illustrato di seguito:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

L'output di esempio seguente mostra due istanze di VM nel set di scalabilità:

```bash
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


La prima colonna dell'output contiene *InstanceId*. Per visualizzare altre informazioni su un'istanza di VM specifica, aggiungere il parametro `--instance-id` ad [az vmss get-instance-view](/cli/azure/vmss#az_vmss_get_instance_view). L'esempio seguente consente di visualizzare informazioni sull'istanza di VM *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Visualizzare l'elenco delle informazioni di connessione
Al servizio di bilanciamento del carico che indirizza il traffico alle singole istanze di VM viene assegnato un indirizzo IP pubblico. Per impostazione predefinita, al servizio di bilanciamento del carico di Azure che inoltra il traffico di connessione remota a ogni VM su una determinata porta vengono aggiunte regole NAT (Network Address Translation). Per connettersi alle istanze di VM di un set di scalabilità, si crea una connessione remota a un indirizzo IP pubblico e un numero di porta assegnati.

Per visualizzare l'elenco con l'indirizzo e le porte per la connessione alle istanze di VM di un set di scalabilità, usare [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

L'output di esempio seguente mostra il nome dell'istanza, l'indirizzo IP pubblico del servizio di bilanciamento del carico e il numero di porta a cui le regole NAT inoltrano il traffico:

```bash
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Connettersi tramite SSH alla prima istanza di VM. Specificare l'indirizzo IP pubblico e il numero di porta con il parametro `-p`, in base a quanto visualizzato dal comando precedente:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Dopo aver effettuato l'accesso all'istanza di VM, è possibile apportare alcune modifiche manuali alla configurazione in base alle esigenze. Per il momento, chiudere la sessione SSH nel modo usuale:

```bash
exit
```


## <a name="understand-vm-instance-images"></a>Informazioni sulle immagini delle istanze di VM
Quando è stato creato un set di scalabilità all'inizio dell'esercitazione, per le istanze di VM è stata specificata un'immagine (`--image`) *UbuntuLTS*. Azure Marketplace include molte immagini utilizzabili per creare istanze di VM. Per visualizzare un elenco delle immagini più usate, eseguire il comando [az vm image list](/cli/azure/vm/image#az_vm_image_list).

```azurecli-interactive
az vm image list --output table
```

L'output di esempio seguente mostra le immagini di VM più comuni in Azure. Il valore di *UrnAlias* può essere usato per specificare una di queste immagini comuni quando si crea un set di scalabilità.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Per visualizzare un elenco completo, aggiungere l'argomento `--all`. L'elenco di immagini può anche essere filtrato per `--publisher` o `–-offer`. Nell'esempio seguente, l'elenco viene filtrato per individuare tutte le immagini con un'offerta corrispondente a *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

L'output sintetico seguente mostra alcune delle immagini CentOS 7.3 disponibili:

```azurecli-interactive 
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Per distribuire un set di scalabilità che usa un'immagine specifica, usare il valore riportato nella colonna *Urn*. Quando si specifica l'immagine, il numero di versione dell'immagine può essere sostituito con *latest* per selezionare l'ultima versione della distribuzione. Nell'esempio seguente, l'argomento `--image` viene usato per specificare l'ultima versione di un'immagine CentOS 7.3.

Dato che la creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti, non è necessario distribuire il set di scalabilità seguente:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>Informazioni sulle dimensioni delle istanze di VM
Una dimensione di istanza di VM, o *SKU*, determina la quantità di risorse di calcolo, come CPU, GPU e memoria, disponibili per l'istanza di VM. È necessario dimensionare le istanze di VM di un set di scalabilità in modo appropriato per il carico di lavoro previsto.

### <a name="vm-instance-sizes"></a>Dimensioni delle istanze di VM
La tabella seguente classifica le dimensioni di VM comuni in base ai casi d'uso.

| type                     | Dimensioni comuni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](../virtual-machines/linux/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo/test e soluzioni di dati e applicazioni medio-piccole.  |
| [Ottimizzate per il calcolo](../virtual-machines/linux/sizes-compute.md)   | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| [Ottimizzate per la memoria](../virtual-machines/linux/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](../virtual-machines/linux/sizes-storage.md)      | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](../virtual-machines/linux/sizes-gpu.md)          | NV, NC            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video.       |
| [Prestazioni elevate](../virtual-machines/linux/sizes-hpc.md) | H, A8-11          | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Trovare le dimensioni di istanze di VM disponibili
Per visualizzare un elenco delle dimensioni di istanze di VM disponibili in una determinata area, usare il comando [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

L'output è simile all'esempio sintetico seguente, che mostra le risorse assegnate a ogni dimensione di VM:

```azurecli-interactive
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Creare un set di scalabilità con una dimensione di istanza di VM specifica
Quando è stato creato un set di scalabilità all'inizio dell'esercitazione, per le istanze di VM è stato usato lo SKU di VM predefinito *Standard_D1_v2*. È possibile specificare una dimensione di istanza di VM diversa in base all'output di [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes). L'esempio seguente creerà un set di scalabilità con il parametro `--vm-sku` per specificare la dimensione di istanza di VM *Standard_F1*. Dato che la creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti, non è necessario distribuire il set di scalabilità seguente:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modificare la capacità di un set di scalabilità
Quando è stato creato un set di scalabilità all'inizio dell'esercitazione, per impostazione predefinita sono state distribuite due istanze di VM. È possibile specificare il parametro `--instance-count` con [az vmss create](/cli/azure/vmss#az_vmss_create) per modificare il numero delle istanze create con un set di scalabilità. Per aumentare o ridurre il numero di istanze di VM nel set di scalabilità esistente, è possibile modificare la capacità manualmente. Il set di scalabilità crea o rimuove il numero necessario di istanze di VM, quindi configura il servizio di bilanciamento del carico per la distribuzione del traffico.

Per aumentare o ridurre manualmente il numero di istanze di VM nel set di scalabilità, usare [az vmss scale](/cli/azure/vmss#az_vmss_scale). L'esempio seguente imposta il numero di istanze di VM nel set di scalabilità su *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Sono necessari alcuni minuti per aggiornare la capacità del set di scalabilità. Per visualizzare il numero di istanze attualmente presente nel set di scalabilità, usare [az vmss show](/cli/azure/vmss#az_vmss_show) ed eseguire una query su *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Attività di gestione comuni
È ora possibile creare un set di scalabilità, visualizzare l'elenco delle informazioni di connessione e connettersi alle istanze di VM. È stato illustrato come è possibile usare una diversa immagine del sistema operativo per le istanze di VM, selezionare una diversa dimensione di VM o ridimensionare manualmente il numero di istanze. Nell'ambito della gestione quotidiana, potrebbe essere necessario arrestare, avviare o riavviare le istanze di VM del set di scalabilità.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Arrestare e deallocare le istanze di VM di un set di scalabilità
Per arrestare una o più istanze di VM di un set di scalabilità, usare [az vmss stop](/cli/azure/vmss#az_vmss_stop). Il parametro `--instance-ids` consente di specificare una o più istanze di VM da arrestare. Se non si specifica un ID istanza, verranno arrestate tutte le istanze di VM del set di scalabilità. L'esempio seguente arresta l'istanza *1*:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

Le istanze di VM arrestate rimangono allocate e continuano a comportare l'addebito di costi di calcolo. Se si preferisce deallocare le istanze di VM mantenendo solo gli addebiti per l'archiviazione, usare [az vmss deallocate](/cli/azure/vmss#az_vmss_deallocate). L'esempio seguente arresta e dealloca l'istanza *1*:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>Avviare le istanze di VM di un set di scalabilità
Per avviare una o più istanze di VM di un set di scalabilità, usare [az vmss start](/cli/azure/vmss#az_vmss_start). Il parametro `--instance-ids` consente di specificare una o più istanze di VM da avviare. Se non si specifica un ID istanza, verranno avviate tutte le istanze di VM del set di scalabilità. L'esempio seguente avvia l'istanza *1*:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Riavviare le istanze di VM di un set di scalabilità
Per riavviare una o più istanze di VM di un set di scalabilità, usare [az vmss restart](/cli/azure/vmss#az_vm_restart). Il parametro `--instance-ids` consente di specificare una o più istanze di VM da riavviare. Se non si specifica un ID istanza, verranno riavviate tutte le istanze di VM del set di scalabilità. L'esempio seguente riavvia l'istanza *1*:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Pulire le risorse
Quando si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute, come le istanze di VM, la rete virtuale e i dischi. Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come eseguire alcune attività di base per la creazione e la gestione dei set di scalabilità con l'interfaccia della riga di comando di Azure 2.0:

> [!div class="checklist"]
> * Creare un set di scalabilità di macchine virtuali e connettersi
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare manualmente un set di scalabilità
> * Eseguire attività comuni per la gestione dei set di scalabilità

Passare all'esercitazione successiva per informazioni sui dischi dei set di scalabilità.

> [!div class="nextstepaction"]
> [Usare dischi di dati con set di scalabilità](tutorial-use-disks-cli.md)
