---
title: Creare una VM Linux suddivisa in zone con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Creare una VM Linux in una zona di disponibilità con l'interfaccia della riga di comando di Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: cdd9910bfef96f56cfa8c8e81363ff9bdb40f444
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328732"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Creare una macchina virtuale Linux in una zona di disponibilità con l'interfaccia della riga di comando di Azure

Questo articolo descrive come usare l'interfaccia della riga di comando di Azure per creare una VM Linux in una zona di disponibilità di Azure. Una [zona di disponibilità](../../availability-zones/az-overview.md) è una zona fisicamente separata in un'area di Azure. Usare le zone di disponibilità per proteggere app e dati da un poco probabile errore o perdita di un intero data center.

Per usare una zona di disponibilità, creare la macchina virtuale in un'[area di Azure supportata](../../availability-zones/az-overview.md#services-support-by-region).

Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di avere eseguito l'accesso a un account di Azure con [az login](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Verificare la disponibilità dello SKU di VM
La disponibilità delle dimensioni delle macchine virtuali, o SKU, può variare in base all'area e alla zona. Per semplificare la pianificazione dell'uso delle zone di disponibilità, è possibile elencare gli SKU di VM disponibili per area e zona di Azure. Questa possibilità assicura la scelta di dimensioni adeguate per le macchine virtuali e l'ottenimento della resilienza desiderata tra le aree. Per altre informazioni sui tipi e sulle dimensioni delle VM, vedere la [panoramica delle dimensioni delle VM](sizes.md).

È possibile visualizzare gli SKU di VM disponibili con il comando [az vm list-skus](/cli/azure/vm). L'esempio seguente elenca gli SKU di VM disponibili nell'area *eastus2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

L'output è simile all'esempio sintetico seguente, che mostra le zone di disponibilità nelle quali è disponibile ogni dimensione di VM:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group).  

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Il gruppo di risorse deve essere creato prima della macchina virtuale. In questo esempio viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *eastus2*. Stati Uniti orientali 2 è una delle aree di Azure che supportano le zone di disponibilità.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Il gruppo di risorse viene specificato quando si crea o si modifica una VM, come viene illustrato in questo articolo.

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Crea una macchina virtuale usando il comando [az vm create](/cli/azure/vm). 

Per la creazione di una macchina virtuale sono disponibili diverse opzioni, ad esempio l'immagine del sistema operativo, il ridimensionamento del disco e le credenziali amministrative. In questo esempio viene creata una macchina virtuale denominata *myVM* che esegue Ubuntu Server. La VM viene creata nella zona disponibilità *1*. Per impostazione predefinita, la VM viene creata nella dimensione *Standard_DS1_v2*.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

La creazione della macchina virtuale può richiedere alcuni minuti. Dopo la creazione della macchina virtuale, l'interfaccia della riga di comando di Azure restituisce informazioni sulla VM. Annotare il valore `zones` che indica la zona di disponibilità in cui è in esecuzione la VM. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Confermare la zona per il disco gestito e un indirizzo IP

Se la macchina virtuale è distribuita in una zona di disponibilità, viene creato un disco gestito per la macchina virtuale nella stessa zona di disponibilità. Per impostazione predefinita, in questa zona viene creato anche un indirizzo IP pubblico. Gli esempi seguenti ottengono informazioni su queste risorse.

Per verificare che disco gestito della macchina virtuale si trovi nella zona di disponibilità, usare il [show di az vm](/cli/azure/vm) comando per restituire l'ID del disco. In questo esempio, l'ID del disco viene archiviato in una variabile che viene usata in un passaggio successivo. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Ora è possibile ottenere informazioni sul disco gestito:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

L'output indica che il disco gestito si trova nella stessa zona di disponibilità della VM:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Usare il comando [az vm list-ip-addresses](/cli/azure/vm) per restituire il nome della risorsa con indirizzo IP pubblico in *myVM*. In questo esempio il nome è archiviato in una variabile che viene usata in un passaggio successivo.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Ora è possibile ottenere informazioni sull'indirizzo IP:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

L'output indica che l'indirizzo IP si trova nella stessa zona di disponibilità della VM:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare una macchina virtuale in una zona di disponibilità. Per altre informazioni, vedere [Aree e disponibilità](regions-and-availability.md) per le VM di Azure.




