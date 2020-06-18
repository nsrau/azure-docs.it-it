---
title: Distribuire macchine virtuali Linux in host dedicati usando l'interfaccia della riga di comando
description: Distribuire macchine virtuali in host dedicati usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: dc772368de1a0f7d8a7d4f44b47ecafda70f0a70
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714849"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Distribuire macchine virtuali in host dedicati usando l'interfaccia della riga di comando di Azure
 

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato l'interfaccia della riga di comando di Azure nella versione 2.0.70 o successiva e di aver eseguito l'accesso a un account Azure usando `az login`. 


## <a name="limitations"></a>Limitazioni

- I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area. Per ulteriori informazioni, fare riferimento alla [pagina dei prezzi](https://aka.ms/ADHPricing) dell'host.

## <a name="create-resource-group"></a>Creare un gruppo di risorse 
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare il gruppo di risorse con az group create. L'esempio seguente crea un gruppo di risorse denominato *myDHResourceGroup* nella località *Stati Uniti orientali*.

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Elencare gli SKU dell'host disponibili in un'area
Non tutti gli SKU dell'host sono disponibili in tutte le aree e nelle zone di disponibilità. 

Elencare la disponibilità dell'host e tutte le restrizioni dell'offerta prima di avviare il provisioning di host dedicati. 

```bash
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Creare un gruppo host 

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È necessario creare un gruppo host in un'area e in una zona di disponibilità e aggiungervi gli host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Estendersi tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera usare.
- Estendersi tra più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, usare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. 

In questo esempio si userà [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando le zone di disponibilità e i domini di errore. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Altri esempi

È anche possibile usare [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host nella zona di disponibilità 1 (e nessun dominio di errore).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Il codice seguente usa [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando solo domini di errore (da usare in aree in cui le zone di disponibilità non sono supportate). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Creare un host 

A questo punto è possibile creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU dell'host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.  

Per altre informazioni sugli SKU e i prezzi degli host, vedere [Prezzi degli host dedicati di Azure](https://aka.ms/ADHPricing).

Usare [az vm host create](/cli/azure/vm/host#az-vm-host-create) per creare un host. Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale 
Creare una macchina virtuale in un host dedicato usando [az vm create](/cli/azure/vm#az-vm-create). Se durante la creazione del gruppo host è stata specificata una zona di disponibilità, è necessario usare la stessa area durante la creazione della macchina virtuale.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Se si crea una macchina virtuale in un host che non dispone di risorse sufficienti, la macchina virtuale verrà creata in uno stato di errore. 


## <a name="check-the-status-of-the-host"></a>Controllare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di macchine virtuali che è ancora possibile distribuire nell'host usando [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 L'output sarà simile al seguente:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Esportare come modello 
È possibile esportare un modello se si desidera creare ora un ambiente di sviluppo aggiuntivo usando gli stessi parametri oppure creare un ambiente di produzione compatibile. Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile creare modelli JSON manualmente o esportare un ambiente esistente per creare il modello JSON desiderato. Usare [az group export](/cli/azure/group#az-group-export) per esportare il gruppo di risorse.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Questo comando crea il file `myDHResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti tutti i nomi delle risorse. È possibile inserire questi nomi nel file del modello aggiungendo il parametro `--include-parameter-default-value` al comando `az group export`. Modificare il modello JSON per specificare i nomi delle risorse, o creare un file parameters.json nel quale vengono specificati i nomi delle risorse.
 
Per creare un ambiente in base al modello, usare [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Eseguire la pulizia 

Viene addebitato il costo per gli host dedicati anche quando non viene distribuita nessuna macchina virtuale. È necessario eliminare tutti gli host attualmente non usati per ridurre i costi.  

È possibile eliminare un host solo quando non sono più presenti macchine virtuali che lo usano. Eliminare le macchine virtuali usando [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Dopo l'eliminazione delle macchine virtuali, è possibile eliminare l'host usando [az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Una volta eliminati tutti gli host, è possibile eliminare il gruppo host usando [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
È anche possibile eliminare l'intero gruppo di risorse in un unico comando. Questa operazione eliminerà tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere la panoramica [Host dedicati](dedicated-hosts.md).

- È anche possibile creare host dedicati usando il [portale di Azure](dedicated-hosts-portal.md).

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.
