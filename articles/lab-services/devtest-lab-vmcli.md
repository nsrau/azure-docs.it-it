---
title: Creare e gestire macchine virtuali in DevTest Labs con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come usare Azure DevTest Labs per creare e gestire le macchine virtuali con l'interfaccia della riga di comando di Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 11ac4e10cbd116ed204a8a11274408f5a5a9b4d9
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183143"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure
Questa Guida introduttiva illustra la creazione, l'avvio, la connessione, l'aggiornamento e la pulizia di un computer di sviluppo nel Lab. 

Prima di iniziare:

* Se il lab non è stato creato, le istruzioni sono disponibili [qui](devtest-lab-create-lab.md).

* [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Per iniziare, eseguire az login per creare una connessione con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creare e verificare la macchina virtuale 
Prima di eseguire i comandi correlati a DevTest Labs, impostare il contesto di Azure appropriato `az account set` usando il comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Il comando per creare una macchina virtuale è: `az lab vm create`. Il gruppo di risorse per il Lab, il nome del Lab e il nome della macchina virtuale sono tutti obbligatori. Gli altri argomenti cambiano a seconda del tipo di macchina virtuale.

Il comando seguente consente di creare un'immagine basata su Windows da Azure Market Place. Il nome dell'immagine è identico a quello visualizzato quando si crea una macchina virtuale usando il portale di Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Il comando seguente crea una macchina virtuale basata su un'immagine personalizzata disponibile nel Lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

L'argomento di **tipo immagine** è stato modificato da **raccolta** a **personalizzata**. Il nome dell'immagine corrisponde a ciò che viene visualizzato se si crea la macchina virtuale nell'portale di Azure.

Il comando seguente crea una macchina virtuale da un'immagine del Marketplace con l'autenticazione SSH:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

È anche possibile creare macchine virtuali basate su formule impostando il parametro del **tipo di immagine** sulla **Formula**. Se è necessario scegliere una rete virtuale specifica per la macchina virtuale, usare i parametri **VNET-Name** e **Subnet** . Per ulteriori informazioni, vedere [AZ Lab VM create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verificare che la VM sia disponibile.
Usare il `az lab vm show` comando per verificare che la macchina virtuale sia disponibile prima di iniziare e connettersi. 

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
Il comando di esempio seguente avvia una macchina virtuale:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Connettersi a una macchina virtuale: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) o [Desktop remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aggiornare la macchina virtuale
Il comando di esempio seguente applica gli artefatti a una macchina virtuale:

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
Il comando di esempio seguente arresta una macchina virtuale.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Eliminare una VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
Vedere il contenuto seguente: [Documentazione dell'interfaccia della riga di comando di Azure per Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 
