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
ms.openlocfilehash: 48a30ef86cdb10b540ffe1231294542ccff87255
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795937"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure
Questa Guida introduttiva guiderà l'utente tramite la creazione, avvio, la connessione, l'aggiornamento e pulizia di un computer di sviluppo nel lab. 

Prima di iniziare:

* Se il lab non è stato creato, le istruzioni sono disponibili [qui](devtest-lab-create-lab.md).

* [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Per iniziare, eseguire az login per creare una connessione con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creare e verificare la macchina virtuale 
Prima di eseguire i comandi correlati a DevTest Labs, impostare il contesto di Azure appropriato usando il `az account set` comando:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Il comando per creare una macchina virtuale è: `az lab vm create`. Il gruppo di risorse per il lab, nome del lab e nome della macchina virtuale sono tutti obbligatori. Il resto degli argomenti cambiare a seconda del tipo di macchina virtuale.

Il comando seguente crea un'immagine basata su Windows da Azure Marketplace. Il nome dell'immagine è lo stesso come verrà visualizzata quando si crea una macchina virtuale usando il portale di Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3’ --admin-username 'AdminUser' --admin-password 'Password1!'
```

Il comando seguente crea una macchina virtuale basata su un'immagine personalizzata disponibile nell'ambiente di laboratorio:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Il **-tipo di immagine** argomento è stato modificato da **gallery** al **personalizzato**. Il nome dell'immagine corrisponde a ciò che viene visualizzato se si intende creare la macchina virtuale nel portale di Azure.

Il comando seguente crea una macchina virtuale da un'immagine di marketplace con ssh autenticazione:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

È anche possibile creare macchine virtuali basate su formule impostando il **-tipo di immagine** parametro per **formula**. Se è necessario scegliere una rete virtuale specifica per la macchina virtuale, usare il **vnet-name** e **subnet** parametri. Per altre informazioni, vedere [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verificare che la VM sia disponibile.
Usare il `az lab vm show` comando per verificare che la macchina virtuale sia disponibile prima di avviare e connettersi a esso. 

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
Il comando seguente avvia una macchina virtuale:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Connettersi a una macchina virtuale: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) oppure [Desktop remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aggiornare la macchina virtuale
Il comando di esempio seguente si applica elementi a una macchina virtuale:

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
Vedere il contenuto seguente: [Documentazione di CLI di Azure per Azure DevTest Labs](/cli/azure/lab?view=azure-cli-latest). 