---
title: Distribuire macchine virtuali Linux ad host dedicati usando l'interfaccia della riga di comandoDeploy Linux VMs to dedicated hosts using the CLI
description: Distribuire macchine virtuali in host dedicati usando l'interfaccia della riga di comando di Azure.Deploy VMs to dedicated hosts using the Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127687"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Distribuire macchine virtuali ad host dedicati usando l'interfaccia della riga di comando di AzureDeploy VMs to dedicated hosts using the Azure CLI
 

Questo articolo illustra come creare un [host dedicato di](dedicated-hosts.md) Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione `az login`2.0.70 o successiva e di aver eseguito l'accesso a un account Azure tramite . 


## <a name="limitations"></a>Limitazioni

- I set di scalabilità delle macchine virtuali non sono attualmente supportati in host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area geografica. Per ulteriori informazioni, consulta la [pagina dei prezzi](https://aka.ms/ADHPricing) host.
 

## <a name="create-resource-group"></a>Creare un gruppo di risorse 
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare il gruppo di risorse con az group create. Nell'esempio seguente viene creato un gruppo di risorse denominato *myDHResourceGroup* nella posizione *Stati Uniti orientali.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Creare un gruppo host 

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. Creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. È possibile utilizzare una o entrambe le seguenti opzioni con gli host dedicati: 
- Estendere più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ciascuna delle zone che si desidera utilizzare.
- Estendetevi su più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario fornire il conteggio dei domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un conteggio dei domini di errore pari a 1. 

È inoltre possibile decidere di utilizzare sia le zone di disponibilità che i domini di errore. 

In questo esempio verrà usato [az vm host group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando sia le zone di disponibilità che i domini di errore. 

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
 
Di seguito viene utilizzato [il gruppo host az vm create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando solo domini di errore (da utilizzare nelle aree in cui le zone di disponibilità non sono supportate). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Creare un hostCreate a host 

Ora creiamo un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.  

Per altre informazioni sugli SKU e sui prezzi dell'host, vedere [Prezzi dell'host dedicato di Azure.For more](https://aka.ms/ADHPricing)information about the host SKUs and pricing, see Azure Dedicated Host pricing .

Usare [az vm host create](/cli/azure/vm/host#az-vm-host-create) per creare un host. Se imposti un numero di domini di errore per il tuo gruppo host, ti verrà chiesto di specificare il dominio di errore per il tuo host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale 
Creare una macchina virtuale all'interno di un host dedicato usando [az vm create](/cli/azure/vm#az-vm-create). Se è stata specificata una zona di disponibilità durante la creazione del gruppo host, è necessario utilizzare la stessa zona durante la creazione della macchina virtuale.

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
> Se si crea una macchina virtuale in un host che non dispone di risorse sufficienti, la macchina virtuale verrà creata in uno stato non riuscito. 


## <a name="check-the-status-of-the-host"></a>Controllare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di macchine virtuali che è comunque possibile distribuire all'host usando [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view).

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
È possibile esportare un modello se si desidera creare un ambiente di sviluppo aggiuntivo con gli stessi parametri o un ambiente di produzione che lo corrisponde. Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile creare modelli JSON manualmente o esportare un ambiente esistente per creare il modello JSON automaticamente. Usare [l'esportazione](/cli/azure/group#az-group-export) del gruppo di ache per esportare il gruppo di risorse.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Questo comando crea il file `myDHResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti tutti i nomi delle risorse. È possibile inserire questi nomi nel file del modello aggiungendo il parametro `--include-parameter-default-value` al comando `az group export`. Modificare il modello JSON per specificare i nomi delle risorse, o creare un file parameters.json nel quale vengono specificati i nomi delle risorse.
 
Per creare un ambiente dal modello, utilizzare [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Eseguire la pulizia 

Vengono addebitati gli host dedicati anche quando non sono distribuite macchine virtuali. È necessario eliminare tutti gli host attualmente non in uso per risparmiare sui costi.  

È possibile eliminare un host solo quando non sono presenti più macchine virtuali che lo utilizzano. Eliminare le macchine virtuali usando [az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Dopo aver eliminato le macchine virtuali, è possibile eliminare l'host utilizzando [az vm host delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Dopo aver eliminato tutti gli host, è possibile eliminare il gruppo host utilizzando [az vm host group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
È inoltre possibile eliminare l'intero gruppo di risorse in un unico comando. Verranno eliminate tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni, vedere la panoramica degli [host dedicati.](dedicated-hosts.md)

- È anche possibile creare host dedicati usando il portale di [Azure.](dedicated-hosts-portal.md)

- È disponibile un modello di esempio, disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che utilizza sia zone che domini di errore per la massima resilienza in un'area.