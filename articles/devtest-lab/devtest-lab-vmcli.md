---
title: Creare e gestire macchine virtuali in DevTest Labs con l&quot;interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per creare e gestire le macchine virtuali con l&quot;interfaccia della riga di comando di Azure 2.0
services: devtest-lab,virtual-machines
documentationcenter: na
author: lisawong19
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: liwong
ms.translationtype: Human Translation
ms.sourcegitcommit: 300958a69fc854cb8db02120a383a4cbbfcacd7b
ms.openlocfilehash: 42b0448c1bcdfa909715abd5075353d63cab8389
ms.contentlocale: it-it
ms.lasthandoff: 05/20/2017


---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure
Questa guida introduttiva illustra la creazione, l'avvio, la connessione, l'aggiornamento e la pulizia di una macchina di sviluppo nel lab. 

Prima di iniziare:

* Se il lab non è stato creato, le istruzioni sono disponibili [qui](devtest-lab-create-lab.md).

* [Installare l'interfaccia della riga di comando 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Per iniziare, eseguire az login per creare una connessione con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creare e verificare la macchina virtuale 
Creare una macchina virtuale da un'immagine del Marketplace con autenticazione SSH.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Inserire il nome del **gruppo di risorse lab** nel parametro --resource-group.
>

Se si vuole creare una macchina virtuale tramite una formula, usare il parametro --formula in [az lab vm create](https://docs.microsoft.com/cli/azure/lab/vm#create).


Verificare che la VM sia disponibile.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Avviare e connettersi alla macchina virtuale
Avviare una VM.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Inserire il nome del **gruppo di risorse lab** nel parametro --resource-group.
>

Connettersi a una VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) o [Desktop remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aggiornare la macchina virtuale
Applicare elementi a una VM.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Elencare gli elementi disponibili nel lab.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Arrestare ed eliminare la macchina virtuale    
Arrestare una VM.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Eliminare una VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
