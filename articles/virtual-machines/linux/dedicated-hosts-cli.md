---
title: Distribuire host dedicati di Azure tramite l'interfaccia della riga di comando | Microsoft Docs
description: Distribuire le macchine virtuali in host dedicati usando l'interfaccia della riga di comando di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/29/2019
ms.author: cynthn
ms.openlocfilehash: 0c060e2ab94c0a57d4d4dc897702e115cfabd9a0
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827283"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>Anteprima: Distribuire macchine virtuali in host dedicati usando l'interfaccia della riga di comando di Azure
 

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.70 o successiva e di aver eseguito l' `az login`accesso a un account Azure usando. 

> [!IMPORTANT]
> Gli host dedicati di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni di anteprima note**
> - I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
> - La versione iniziale di anteprima supporta la serie di macchine virtuali seguente: DSv3 e ESv3. 
 

## <a name="create-resource-group"></a>Crea gruppo di risorse 
Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare il gruppo di risorse con AZ Group create. Nell'esempio seguente viene creato un gruppo di risorse denominato *myDHResourceGroup* nella località *Stati Uniti orientali* .

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Creazione di un gruppo host 

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È possibile creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Span tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera utilizzare.
- Si estende su più domini di errore di cui è stato eseguito il mapping ai rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. 

In questo esempio si userà [AZ VM host Group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando sia le zone di disponibilità che i domini di errore. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Altri esempi

È anche possibile usare [AZ VM host Group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host nella zona di disponibilità 1 (e nessun dominio di errore).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Il codice seguente usa [AZ VM host Group create](/cli/azure/vm/host/group#az-vm-host-group-create) per creare un gruppo host usando solo i domini di errore (da usare nelle aree in cui le zone di disponibilità non sono supportate). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Creazione di un host 

A questo punto è possibile creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.  Durante l'anteprima, sono supportati i valori SKU host seguenti: DSv3_Type1 e ESv3_Type1.


Per altre informazioni sugli SKU e i prezzi degli host, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).

Usare [AZ VM host create](/cli/azure/vm/host#az-vm-host-create) per creare un host. Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale 
Creare una macchina virtuale in un host dedicato usando [AZ VM create](/cli/azure/vm#az-vm-create). Se durante la creazione del gruppo host è stata specificata una zona di disponibilità, è necessario usare la stessa area durante la creazione della macchina virtuale.

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


## <a name="check-the-status-of-the-host"></a>Verificare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di macchine virtuali che è ancora possibile distribuire nell'host usando [AZ VM host Get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

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
È possibile esportare un modello se si vuole creare un ambiente di sviluppo aggiuntivo con gli stessi parametri o un ambiente di produzione corrispondente. Azure Resource Manager utilizza i modelli JSON che definiscono tutti i parametri per l'ambiente. È possibile creare interi ambienti facendo riferimento a questo modello JSON. È possibile compilare modelli JSON manualmente o esportare un ambiente esistente per creare il modello JSON. Usare il comando [AZ Group Export](/cli/azure/group#az-group-export) per esportare il gruppo di risorse.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Questo comando crea il file `myDHResourceGroup.json` nella directory di lavoro corrente. Quando si crea un ambiente da questo modello, vengono richiesti tutti i nomi delle risorse. È possibile inserire questi nomi nel file del modello aggiungendo il parametro `--include-parameter-default-value` al comando `az group export`. Modificare il modello JSON per specificare i nomi delle risorse oppure creare un file Parameters. JSON che specifichi i nomi delle risorse.
 
Per creare un ambiente dal modello, usare [AZ Group Deployment create](/cli/azure/group/deployment#az-group-deployment-create).

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Eseguire la pulizia 

Viene addebitato il costo per gli host dedicati anche quando nessuna macchina virtuale viene distribuita. È necessario eliminare tutti gli host attualmente non utilizzati per ridurre i costi.  

È possibile eliminare un host solo quando non sono più presenti macchine virtuali che lo utilizzano. Eliminare le macchine virtuali usando il comando [AZ VM Delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Dopo l'eliminazione delle macchine virtuali, è possibile eliminare l'host usando [AZ VM host Delete](/cli/azure/vm/host#az-vm-host-delete).

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Una volta eliminati tutti gli host, è possibile eliminare il gruppo host usando [AZ VM host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
È anche possibile eliminare l'intero gruppo di risorse in un unico comando. Questa operazione eliminerà tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere Cenni preliminari sugli [host dedicati](dedicated-hosts.md) .

- È anche possibile creare host dedicati usando il [portale di Azure](dedicated-hosts-portal.md).

- È disponibile un [modello di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che usa sia le zone che i domini di errore per la resilienza massima in un'area.