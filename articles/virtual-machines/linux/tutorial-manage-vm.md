---
title: Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: 'Esercitazione: creare e gestire VM Linux con l''interfaccia della riga di comando di Azure'
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
ms.openlocfilehash: b2e9324cbe7ae683a472ecc0ee93329773886f88
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure

Le macchine virtuali di Azure offrono un ambiente di elaborazione completamente configurabile e flessibile. Questa esercitazione illustra gli elementi di base della distribuzione di una macchina virtuale di Azure, ad esempio la selezione delle dimensioni di una VM, la selezione dell'immagine di una VM e la distribuzione di una VM. Si apprenderà come:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare una VM
> * Visualizzare e comprendere lo stato di una macchina virtuale


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *eastus*. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

Il gruppo di risorse viene specificato quando si crea o si modifica una VM, come viene illustrato in questa esercitazione.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Crea una macchina virtuale usando il comando [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create). 

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, il ridimensionamento del disco e le credenziali amministrative. In questo esempio viene creata una macchina virtuale denominata *myVM* che esegue Ubuntu Server. 

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

La creazione della macchina virtuale può richiedere alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure restituisce informazioni sulla VM. Prendere nota dell'indirizzo `publicIpAddress`, che può essere usato per accedere alla macchina virtuale. 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Connettersi alla macchina virtuale

È ora possibile connettersi alla macchina virtuale con SSH in Azure Cloud Shell o dal computer locale. Sostituire l'indirizzo IP di esempio con l'indirizzo `publicIpAddress` annotato nel passaggio precedente.

```bash
ssh 52.174.34.95
```

Dopo aver eseguito l'accesso alla macchina virtuale, sarà possibile installare e configurare le applicazioni. Al termine si chiude la sessione SSH come di consueto:

```bash
exit
```

## <a name="understand-vm-images"></a>Informazioni sulle immagini delle VM

Azure Marketplace include diverse immagini che possono essere usate per creare VM. Nei passaggi precedenti è stata creata una macchina virtuale usando un'immagine Ubuntu. In questo passaggio l'interfaccia della riga di comando di Azure viene usata per cercare nel marketplace un'immagine CentOS, che viene quindi usata per distribuire una seconda macchina virtuale.  

Per visualizzare un elenco delle immagini più usate, eseguire il comando [az vm image list](/cli/azure/vm/image#az_vm_image_list).

```azurecli-interactive 
az vm image list --output table
```

L'output del comando restituisce le immagini di macchina virtuale più popolari in Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Per visualizzare l'elenco completo, aggiungere l'argomento `--all`. L'elenco di immagini può anche essere filtrato per `--publisher` o `–-offer`. In questo esempio l'elenco viene filtrato per cercare tutte le immagini con un'offerta corrispondente a *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Output parziale:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Per distribuire una VM usando un'immagine specifica, prendere nota del valore nella colonna *Urn*. Quando si specifica l'immagine, il numero di versione dell'immagine può essere sostituito con "latest", che seleziona la versione più recente della distribuzione. In questo esempio viene usato l'argomento `--image` per specificare la versione più recente di un'immagine CentOS 6.5.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Informazioni sulle dimensioni delle VM

La dimensioni di una macchina virtuale determinano la quantità di risorse di calcolo, ad esempio CPU, GPU e memoria, disponibili per la macchina virtuale. È necessario ridimensionare le macchine virtuali in modo appropriato per il carico di lavoro previsto. Se aumenta il carico di lavoro, è possibile ridimensionare una macchina virtuale esistente.

### <a name="vm-sizes"></a>Dimensioni delle VM

La tabella seguente classifica le dimensioni a seconda dei casi d'uso.  

| type                     | Dimensioni           |    DESCRIZIONE       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Utilizzo generico](sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Rapporto equilibrato tra CPU e memoria. Soluzione ideale per sviluppo/test e soluzioni di dati e applicazioni medio-piccole.  |
| [Ottimizzate per il calcolo](sizes-compute.md)   | Fs, F             | Rapporto elevato tra CPU e memoria. Soluzione idonea per applicazioni con livelli medi di traffico, dispositivi di rete e processi batch.        |
| [Ottimizzate per la memoria](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Rapporto elevato tra memoria e core. Soluzione ideale per database relazionali, cache medio-grandi e analisi in memoria.                 |
| [Ottimizzate per l'archiviazione](../virtual-machines-windows-sizes-storage.md)      | Ls                | I/O e velocità effettiva del disco elevati. Ideale per Big Data, database SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | VM specializzate ottimizzate per livelli intensivi di rendering della grafica e modifica di video.       |
| [Prestazioni elevate](sizes-hpc.md) | H, A8-11          | Le VM con CPU più potenti, con interfacce di rete ad alta velocità effettiva opzionali (RDMA). 


### <a name="find-available-vm-sizes"></a>Trovare le dimensioni delle macchine virtuali disponibili

Per visualizzare un elenco delle dimensioni delle VM disponibili in una determinata area, usare il comando [az vm list-sizes](/cli/azure/vm#az_vm_list_sizes). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Output parziale:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Creare una macchina virtuale con dimensioni specifiche

Nell'esempio precedente di creazione di una VM, non essendo state specificate le dimensioni, sono state usate le dimensioni predefinite. Le dimensioni di una VM possono essere selezionate in fase di creazione usando [az vm create](/cli/azure/vm#az_vm_create) e l'argomento `--size`. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Ridimensionare una VM

Dopo la distribuzione di una VM, è possibile ridimensionarla per aumentare o ridurre l'allocazione delle risorse. È possibile visualizzare la dimensione attuale di una macchina virtuale con [az vm show](/cli/azure/vm#az_vm_show):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Prima di ridimensionare una macchina virtuale, verificare se le dimensioni desiderate sono disponibili nel cluster di Azure corrente. Il comando [az vm list-vm-resize-options](/cli/azure/vm#az_vm_list_vm_resize_options) restituisce l'elenco di dimensioni. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Se le dimensioni desiderate sono disponibili, la VM può essere ridimensionata mentre è accesa, ma durante l'operazione viene riavviata. Usare il comando [az vm resize]( /cli/azure/vm#az_vm_resize) per eseguire il ridimensionamento.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Se nel cluster corrente non sono disponibili le dimensioni desiderate, è necessario deallocare la VM prima di poter eseguire l'operazione di ridimensionamento. Usare il comando [az vm deallocate]( /cli/azure/vm#az_vm_deallocate) per arrestare e deallocare la VM. Tenere presente che, quando la VM viene riaccesa, i dati sul disco temporaneo potrebbero essere rimossi. Anche l'indirizzo IP pubblico viene modificato a meno che non venga usato un indirizzo IP statico. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Dopo la deallocazione, è possibile eseguire il ridimensionamento. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Dopo il ridimensionamento, la VM può essere avviata.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Stati di alimentazione di una macchina virtuale

Una macchina virtuale di Azure può avere uno dei diversi stati di alimentazione. Questo stato rappresenta lo stato corrente della VM dal punto di vista dell'hypervisor. 

### <a name="power-states"></a>Stati di alimentazione

| Stato di alimentazione | DESCRIZIONE
|----|----|
| Avvio in corso | Indica che è in corso l'avvio della macchina virtuale. |
| In esecuzione | Indica che la macchina virtuale è in esecuzione. |
| Stopping | Indica che è in corso l'arresto della macchina virtuale. | 
| Arrestato | Indica che la macchina virtuale è stata arrestata. Alle macchine virtuali con stato arrestato continuano a essere addebitati i costi di calcolo.  |
| Deallocazione | Indica che è in corso la deallocazione della macchina virtuale. |
| Deallocato | Indica che la macchina virtuale è stata rimossa dall'hypervisor, ma è ancora disponibile nel piano di controllo. Alle macchine virtuali con stato deallocato non vengono addebitati i costi di calcolo. |
| - | Indica che lo stato di alimentazione della macchina virtuale è sconosciuto. |

### <a name="find-power-state"></a>Trovare lo stato di alimentazione

Per recuperare lo stato di una determinata VM, usare il comando [az vm get instance-view](/cli/azure/vm#az_vm_get_instance_view). Assicurarsi di specificare un nome valido per una macchina virtuale e un gruppo di risorse. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Output:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Attività di gestione

Durante il ciclo di vita di una macchina virtuale si eseguono attività di gestione come l'avvio, l'arresto o l'eliminazione della macchina virtuale. È consigliabile creare script per automatizzare le attività ripetitive o complesse. Usando l'interfaccia della riga di comando di Azure è possibile eseguire molte attività di gestione comuni dalla riga di comando o tramite script. 

### <a name="get-ip-address"></a>Ottenere l'indirizzo IP

Questo comando restituisce gli indirizzi IP pubblici e privati di una macchina virtuale.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Arrestare la macchina virtuale

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Avviare la macchina virtuale

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Eliminare un gruppo di risorse

Se si elimina un gruppo di risorse, vengono eliminate anche tutte le risorse in esso contenute, ad esempio la macchina virtuale, la rete virtuale e il disco. Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono illustrate la creazione e la gestione di VM di base, ad esempio:

> [!div class="checklist"]
> * Creare e connettersi a una macchina virtuale
> * Selezionare e usare le immagini di una macchina virtuale
> * Visualizzare e usare macchine virtuali di dimensioni specifiche
> * Ridimensionare una VM
> * Visualizzare e comprendere lo stato di una macchina virtuale

Passare all'esercitazione successiva per informazioni sui dischi di macchine virtuali.  

> [!div class="nextstepaction"]
> [Creare e gestire dischi di macchine virtuali](./tutorial-manage-disks.md)
